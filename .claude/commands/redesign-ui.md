---
description: Transform the UI from top-nav layout to a SaaS-style vertical sidebar layout
---

# Redesign UI: Top Nav to Sidebar Layout

Transform the application from its current horizontal top-navigation layout into a modern SaaS-style layout with a persistent vertical sidebar and a thin top utility bar.

**IMPORTANT**: This task requires significantly modifying multiple .vue files. You MUST delegate all .vue file changes to the `vue-expert` subagent. Break the work into the ordered steps below.

## Target Layout

```
+--sidebar--+------top-bar--------------------+
|            |                                 |
|  [Logo]    |  Page Title    [Lang] [Profile] |
|            +---------------------------------+
|  Overview  |                                 |
|  Inventory |  [FilterBar — inline, not sticky]|
|  Orders    |                                 |
|  Finance   |  [Page Content / router-view]   |
|  Demand    |                                 |
|  Reports   |                                 |
|            |                                 |
|  --------  |                                 |
|  [User]    |                                 |
+------------+---------------------------------+
```

## Design Specifications

### 1. Sidebar (left column, full viewport height)

- **Width**: 240px, fixed — does not stretch or shrink
- **Background**: White (#ffffff) with a 1px right border (#e2e8f0)
- **Position**: Fixed to the left, full viewport height, z-index 100
- **Content (top to bottom)**:
  - **Logo area**: Company name via `t('nav.companyName')` as a compact wordmark. Subtitle via `t('nav.subtitle')` in secondary text (#64748b). Generous padding (~1.5rem). Separated from nav by a subtle divider.
  - **Navigation links**: All 6 current routes as vertical items. Each item has an inline SVG icon (20x20) to the left of the label text. Use existing `t('nav.*')` translation keys.
    - **Active state**: 3px left border in primary blue (#2563eb), background tint (#eff6ff), text and icon in #2563eb
    - **Hover state**: Background #f1f5f9, text #0f172a
    - **Default**: Text #64748b, icon #94a3b8
  - **User section** (pinned to bottom via flex): Compact card with user avatar (initials circle), name, and job title. Separated from nav by a top border. Clicking opens the profile dropdown (reuse ProfileMenu logic).

**Nav item icons** (simple stroke-based inline SVGs, 20x20 viewBox):
- Overview: 4-square grid (dashboard)
- Inventory: Box/package
- Orders: Clipboard with list
- Finance: Bar chart
- Demand Forecast: Trending-up line
- Reports: Document/file

### 2. Top Bar (spans from sidebar right edge to viewport right)

- **Height**: ~56px
- **Background**: White (#ffffff) with a 1px bottom border (#e2e8f0)
- **Position**: Sticky at top within the main content column (does not overlap sidebar)
- **Left**: Page title derived from current route name (e.g., "Overview", "Inventory")
- **Right**: LanguageSwitcher component and a compact profile/notification area

### 3. Main Content Area (right of sidebar, below top bar)

- **Width**: Remaining viewport space (100% minus 240px sidebar)
- **Background**: #f8fafc
- **FilterBar**: Renders inline at top of content area — no longer sticky, no longer fixed at `top: 70px`. Just a normal flow element with bottom margin.
- **`<router-view />`**: Renders below FilterBar
- **Padding**: 1.5rem to 2rem
- **Max width**: Remove or increase the 1600px constraint — let content use available width

### 4. Mobile (viewport < 768px)

- Sidebar collapses off-screen (transform: translateX(-100%))
- Hamburger button appears in top-left of top bar to toggle sidebar
- Open sidebar overlays content with a semi-transparent backdrop
- Clicking a nav item or the backdrop closes the sidebar

### 5. Typography

- Company name: 1.125rem, weight 700, color #0f172a
- Subtitle: 0.75rem, weight 400, color #64748b
- Nav labels: 0.875rem, weight 500
- User name: 0.875rem, weight 600
- User title: 0.75rem, color #64748b
- Page title in top bar: 1.25rem, weight 700

## Files to Modify

### `client/src/App.vue` — MAJOR

**Template**: Replace `<header class="top-nav">` and horizontal nav with the 3-zone sidebar layout using CSS Grid or Flexbox. Move nav links into a vertical sidebar list. Move LanguageSwitcher into top bar. Move ProfileMenu into sidebar bottom. Place FilterBar inside the main content column above `<router-view />`. Keep both modal components (ProfileDetailsModal, TasksModal) unchanged — they use Teleport.

**Script**: Add a computed property to derive page title from `$route.path`. Add a `sidebarOpen` ref for mobile toggle. Keep all existing task/profile logic.

**Styles**: Remove `.top-nav`, `.nav-container`, `.nav-tabs` styles. Add new `.app-layout`, `.sidebar`, `.sidebar-logo`, `.sidebar-nav`, `.sidebar-nav-item`, `.sidebar-user`, `.top-bar`, `.main-content` styles. **Preserve all global utility CSS** (`.card`, `.badge`, `.stat-card`, `.table-container`, tables, `.loading`, `.error`, `.page-header`, `.stats-grid`) — these are used by view components.

### `client/src/components/FilterBar.vue` — MINOR

Remove `position: sticky`, `top: 70px`, and `z-index: 90` from `.filters-bar`. Make it a normal flow element with appropriate bottom margin. Keep all filter logic unchanged.

### `client/src/components/ProfileMenu.vue` — MINOR

Adjust dropdown positioning for sidebar context — dropdown should open upward or to the right. Keep all functionality identical.

### `client/src/components/LanguageSwitcher.vue` — MINIMAL

Minor style tweaks for top bar context if needed. Functionality unchanged.

### No changes expected

- `client/src/main.js` — Router stays the same
- `client/src/views/*.vue` — Views use global utility CSS, should work without changes
- `client/src/composables/*` — No changes needed

## Execution Order

Execute these steps in order, delegating each to `vue-expert`:

### Step 1: Update FilterBar
Remove sticky positioning from FilterBar.vue. Safe, isolated change.

### Step 2: Restructure App.vue
Main task. Rewrite App.vue template to sidebar+topbar+content layout. Rewrite layout styles. Add page title computed and mobile toggle ref. Preserve all global utility CSS and all existing script logic.

### Step 3: Adjust Component Positioning
Adjust ProfileMenu dropdown direction for sidebar placement. Minor LanguageSwitcher tweaks for top bar.

### Step 4: Visual Polish
Fine-tune spacing, hover transitions on nav items, smooth slide-in animation for mobile sidebar. Ensure consistent look across all pages.

## Verification

After all changes:

1. **Visual check**: Use Playwright to screenshot `http://localhost:3000` at 1280x800. Verify sidebar on left, content on right.
2. **Navigation**: Click each sidebar nav link — verify correct view loads and active state updates.
3. **Filters**: Verify FilterBar renders inline and filters still work (change a filter, confirm data updates).
4. **Profile/Tasks**: Click user section in sidebar — verify dropdown works. Open Profile Details and Tasks modals.
5. **Language**: Switch language via top bar LanguageSwitcher — verify sidebar labels update.
6. **Mobile**: Playwright at 375x812 — verify sidebar hidden, hamburger visible, toggle works.
7. **Page integrity**: Visit each page (Dashboard, Inventory, Orders, Spending, Demand, Reports) and verify cards, tables, and charts render correctly.
