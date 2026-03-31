<template>
  <div class="app-layout">
    <!-- Mobile backdrop -->
    <div v-if="sidebarOpen" class="sidebar-backdrop" @click="sidebarOpen = false"></div>

    <!-- Sidebar -->
    <aside class="sidebar" :class="{ open: sidebarOpen }">
      <div class="sidebar-logo">
        <h1>{{ t('nav.companyName') }}</h1>
        <span class="sidebar-subtitle">{{ t('nav.subtitle') }}</span>
      </div>

      <nav class="sidebar-nav">
        <router-link
          to="/"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <rect x="2" y="2" width="7" height="7" rx="1"/>
            <rect x="11" y="2" width="7" height="7" rx="1"/>
            <rect x="2" y="11" width="7" height="7" rx="1"/>
            <rect x="11" y="11" width="7" height="7" rx="1"/>
          </svg>
          <span>{{ t('nav.overview') }}</span>
        </router-link>

        <router-link
          to="/inventory"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/inventory' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <path d="M3 7l7-4 7 4v8l-7 4-7-4V7z"/>
            <path d="M3 7l7 4 7-4"/>
            <path d="M10 11v8"/>
          </svg>
          <span>{{ t('nav.inventory') }}</span>
        </router-link>

        <router-link
          to="/orders"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/orders' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <rect x="4" y="2" width="12" height="16" rx="1.5"/>
            <path d="M7 6h6M7 9h6M7 12h4"/>
          </svg>
          <span>{{ t('nav.orders') }}</span>
        </router-link>

        <router-link
          to="/spending"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/spending' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <rect x="3" y="10" width="3" height="7" rx="0.5"/>
            <rect x="8.5" y="6" width="3" height="11" rx="0.5"/>
            <rect x="14" y="3" width="3" height="14" rx="0.5"/>
          </svg>
          <span>{{ t('nav.finance') }}</span>
        </router-link>

        <router-link
          to="/demand"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/demand' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <path d="M3 17l4-6 4 3 6-11"/>
            <path d="M14 3h3v3"/>
          </svg>
          <span>{{ t('nav.demandForecast') }}</span>
        </router-link>

        <router-link
          to="/reports"
          class="sidebar-nav-item"
          :class="{ active: $route.path === '/reports' }"
          @click="sidebarOpen = false"
        >
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <path d="M5 2h7l4 4v12a1 1 0 01-1 1H5a1 1 0 01-1-1V3a1 1 0 011-1z"/>
            <path d="M12 2v4h4"/>
            <path d="M7 10h6M7 13h4"/>
          </svg>
          <span>Reports</span>
        </router-link>
      </nav>

      <div class="sidebar-user" @click="toggleProfileMenu">
        <div class="sidebar-user-avatar">
          {{ getInitials(currentUser.name) }}
        </div>
        <div class="sidebar-user-info">
          <div class="sidebar-user-name">{{ currentUser.name }}</div>
          <div class="sidebar-user-title">{{ currentUser.jobTitle }}</div>
        </div>
        <svg width="16" height="16" viewBox="0 0 16 16" fill="none" class="sidebar-user-chevron" :class="{ 'chevron-open': profileMenuOpen }">
          <path d="M4 6L8 10L12 6" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
        </svg>

        <div v-if="profileMenuOpen" class="sidebar-profile-dropdown" @click.stop>
          <button class="sidebar-dropdown-item" @mousedown.prevent="handleShowProfileDetails">
            <svg width="16" height="16" viewBox="0 0 18 18" fill="none">
              <path d="M9 9C10.6569 9 12 7.65685 12 6C12 4.34315 10.6569 3 9 3C7.34315 3 6 4.34315 6 6C6 7.65685 7.34315 9 9 9Z" stroke="currentColor" stroke-width="1.5"/>
              <path d="M15 15C15 12.7909 12.3137 11 9 11C5.68629 11 3 12.7909 3 15" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
            </svg>
            {{ t('profile.profileDetails') }}
          </button>
          <button class="sidebar-dropdown-item" @mousedown.prevent="handleShowTasks">
            <svg width="16" height="16" viewBox="0 0 18 18" fill="none">
              <path d="M15 3H3C2.44772 3 2 3.44772 2 4V14C2 14.5523 2.44772 15 3 15H15C15.5523 15 16 14.5523 16 14V4C16 3.44772 15.5523 3 15 3Z" stroke="currentColor" stroke-width="1.5"/>
              <path d="M6 7L8 9L12 5" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            {{ t('profile.myTasks') }}
            <span v-if="pendingTaskCount > 0" class="sidebar-task-badge">{{ pendingTaskCount }}</span>
          </button>
          <div class="sidebar-dropdown-divider"></div>
          <button class="sidebar-dropdown-item sidebar-dropdown-logout" @mousedown.prevent="handleLogout">
            <svg width="16" height="16" viewBox="0 0 18 18" fill="none">
              <path d="M7 15H4C3.44772 15 3 14.5523 3 14V4C3 3.44772 3.44772 3 4 3H7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round"/>
              <path d="M11 12L15 9M15 9L11 6M15 9H7" stroke="currentColor" stroke-width="1.5" stroke-linecap="round" stroke-linejoin="round"/>
            </svg>
            {{ t('profile.logout') }}
          </button>
        </div>
      </div>
    </aside>

    <!-- Main area (right of sidebar) -->
    <div class="main-wrapper">
      <!-- Top bar -->
      <header class="top-bar">
        <button class="mobile-menu-btn" @click="sidebarOpen = !sidebarOpen">
          <svg width="20" height="20" viewBox="0 0 20 20" fill="none" stroke="currentColor" stroke-width="1.5">
            <path d="M3 5h14M3 10h14M3 15h14" stroke-linecap="round"/>
          </svg>
        </button>
        <h2 class="page-title">{{ pageTitle }}</h2>
        <div class="top-bar-actions">
          <LanguageSwitcher />
        </div>
      </header>

      <!-- Content -->
      <main class="main-content">
        <FilterBar />
        <router-view />
      </main>
    </div>

    <ProfileDetailsModal
      :is-open="showProfileDetails"
      @close="showProfileDetails = false"
    />

    <TasksModal
      :is-open="showTasks"
      :tasks="tasks"
      @close="showTasks = false"
      @add-task="addTask"
      @delete-task="deleteTask"
      @toggle-task="toggleTask"
    />
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted, computed, watch } from 'vue'
import { useRoute } from 'vue-router'
import { api } from './api'
import { useAuth } from './composables/useAuth'
import { useI18n } from './composables/useI18n'
import FilterBar from './components/FilterBar.vue'
import ProfileDetailsModal from './components/ProfileDetailsModal.vue'
import TasksModal from './components/TasksModal.vue'
import LanguageSwitcher from './components/LanguageSwitcher.vue'

export default {
  name: 'App',
  components: {
    FilterBar,
    ProfileDetailsModal,
    TasksModal,
    LanguageSwitcher
  },
  setup() {
    const { currentUser, getInitials, logout } = useAuth()
    const { t } = useI18n()
    const route = useRoute()

    const showProfileDetails = ref(false)
    const showTasks = ref(false)
    const sidebarOpen = ref(false)
    const profileMenuOpen = ref(false)
    const apiTasks = ref([])

    const pageTitle = computed(() => {
      const titles = {
        '/': t('nav.overview'),
        '/inventory': t('nav.inventory'),
        '/orders': t('nav.orders'),
        '/spending': t('nav.finance'),
        '/demand': t('nav.demandForecast'),
        '/reports': 'Reports'
      }
      return titles[route.path] || 'Overview'
    })

    // Merge mock tasks from currentUser with API tasks
    const tasks = computed(() => {
      return [...currentUser.value.tasks, ...apiTasks.value]
    })

    const pendingTaskCount = computed(() => {
      return tasks.value.filter(task => task.status === 'pending').length
    })

    const toggleProfileMenu = () => {
      profileMenuOpen.value = !profileMenuOpen.value
    }

    const handleShowProfileDetails = () => {
      profileMenuOpen.value = false
      showProfileDetails.value = true
    }

    const handleShowTasks = () => {
      profileMenuOpen.value = false
      showTasks.value = true
    }

    const handleLogout = () => {
      profileMenuOpen.value = false
      logout()
    }

    const loadTasks = async () => {
      try {
        apiTasks.value = await api.getTasks()
      } catch (err) {
        console.error('Failed to load tasks:', err)
      }
    }

    const addTask = async (taskData) => {
      try {
        const newTask = await api.createTask(taskData)
        apiTasks.value.unshift(newTask)
      } catch (err) {
        console.error('Failed to add task:', err)
      }
    }

    const deleteTask = async (taskId) => {
      try {
        const isMockTask = currentUser.value.tasks.some(t => t.id === taskId)

        if (isMockTask) {
          const index = currentUser.value.tasks.findIndex(t => t.id === taskId)
          if (index !== -1) {
            currentUser.value.tasks.splice(index, 1)
          }
        } else {
          await api.deleteTask(taskId)
          apiTasks.value = apiTasks.value.filter(t => t.id !== taskId)
        }
      } catch (err) {
        console.error('Failed to delete task:', err)
      }
    }

    const toggleTask = async (taskId) => {
      try {
        const mockTask = currentUser.value.tasks.find(t => t.id === taskId)

        if (mockTask) {
          mockTask.status = mockTask.status === 'pending' ? 'completed' : 'pending'
        } else {
          const updatedTask = await api.toggleTask(taskId)
          const index = apiTasks.value.findIndex(t => t.id === taskId)
          if (index !== -1) {
            apiTasks.value[index] = updatedTask
          }
        }
      } catch (err) {
        console.error('Failed to toggle task:', err)
      }
    }

    const handleClickOutside = (event) => {
      if (profileMenuOpen.value) {
        const userEl = document.querySelector('.sidebar-user')
        if (userEl && !userEl.contains(event.target)) {
          profileMenuOpen.value = false
        }
      }
    }

    onMounted(() => {
      document.addEventListener('click', handleClickOutside)
      loadTasks()
    })

    onUnmounted(() => {
      document.removeEventListener('click', handleClickOutside)
    })

    watch(() => route.path, () => {
      profileMenuOpen.value = false
    })

    return {
      t,
      currentUser,
      getInitials,
      logout,
      route,
      sidebarOpen,
      profileMenuOpen,
      pageTitle,
      showProfileDetails,
      showTasks,
      tasks,
      pendingTaskCount,
      toggleProfileMenu,
      handleShowProfileDetails,
      handleShowTasks,
      handleLogout,
      addTask,
      deleteTask,
      toggleTask
    }
  }
}
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, Oxygen, Ubuntu, Cantarell, sans-serif;
  background: #f8fafc;
  color: #1e293b;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

/* Layout */
.app-layout {
  display: flex;
  min-height: 100vh;
}

/* Sidebar */
.sidebar {
  width: 240px;
  min-width: 240px;
  background: #ffffff;
  border-right: 1px solid #e2e8f0;
  display: flex;
  flex-direction: column;
  position: fixed;
  top: 0;
  left: 0;
  bottom: 0;
  z-index: 100;
  transition: box-shadow 0.2s ease;
}

.sidebar-logo {
  padding: 1.5rem;
  border-bottom: 1px solid #e2e8f0;
}

.sidebar-logo h1 {
  font-size: 1.125rem;
  font-weight: 700;
  color: #0f172a;
  margin: 0;
  letter-spacing: -0.025em;
}

.sidebar-subtitle {
  font-size: 0.75rem;
  color: #64748b;
  margin-top: 0.125rem;
  display: block;
}

/* Sidebar Navigation */
.sidebar-nav {
  flex: 1;
  padding: 0.75rem 0;
  overflow-y: auto;
}

.sidebar-nav-item {
  display: flex;
  align-items: center;
  gap: 0.75rem;
  padding: 0.6rem 1.25rem;
  margin: 0.125rem 0.5rem;
  border-radius: 6px;
  font-size: 0.875rem;
  font-weight: 500;
  color: #64748b;
  text-decoration: none;
  transition: all 0.15s ease;
  border-left: 3px solid transparent;
}

.sidebar-nav-item svg {
  color: #94a3b8;
  flex-shrink: 0;
  transition: color 0.15s ease;
}

.sidebar-nav-item:hover {
  background: #f1f5f9;
  color: #0f172a;
}

.sidebar-nav-item:hover svg {
  color: #64748b;
}

.sidebar-nav-item.active {
  border-left-color: #2563eb;
  background: #eff6ff;
  color: #2563eb;
}

.sidebar-nav-item.active svg {
  color: #2563eb;
}

/* Sidebar User */
.sidebar-user {
  padding: 1rem 1.25rem;
  border-top: 1px solid #e2e8f0;
  display: flex;
  align-items: center;
  gap: 0.75rem;
  cursor: pointer;
  transition: background 0.15s ease;
  position: relative;
}

.sidebar-user:hover {
  background: #f8fafc;
}

.sidebar-user-avatar {
  width: 36px;
  height: 36px;
  border-radius: 50%;
  background: #2563eb;
  color: #fff;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 0.8rem;
  font-weight: 600;
  flex-shrink: 0;
}

.sidebar-user-info {
  overflow: hidden;
  flex: 1;
}

.sidebar-user-name {
  font-size: 0.875rem;
  font-weight: 600;
  color: #0f172a;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-user-title {
  font-size: 0.75rem;
  color: #64748b;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.sidebar-user-chevron {
  color: #94a3b8;
  flex-shrink: 0;
  transition: transform 0.2s ease;
}

.sidebar-user-chevron.chevron-open {
  transform: rotate(180deg);
}

.sidebar-profile-dropdown {
  position: absolute;
  bottom: calc(100% + 0.5rem);
  left: 0.5rem;
  right: 0.5rem;
  background: white;
  border: 1px solid #e2e8f0;
  border-radius: 8px;
  box-shadow: 0 -4px 20px rgba(0, 0, 0, 0.08);
  z-index: 200;
  overflow: hidden;
}

.sidebar-dropdown-item {
  width: 100%;
  display: flex;
  align-items: center;
  gap: 0.625rem;
  padding: 0.625rem 0.875rem;
  background: none;
  border: none;
  text-align: left;
  cursor: pointer;
  transition: background 0.15s ease;
  font-family: inherit;
  font-size: 0.813rem;
  font-weight: 500;
  color: #334155;
}

.sidebar-dropdown-item:hover {
  background: #f8fafc;
}

.sidebar-dropdown-item svg {
  color: #64748b;
  flex-shrink: 0;
}

.sidebar-dropdown-divider {
  height: 1px;
  background: #e2e8f0;
  margin: 0.25rem 0;
}

.sidebar-dropdown-logout {
  color: #dc2626;
}

.sidebar-dropdown-logout svg {
  color: #dc2626;
}

.sidebar-dropdown-logout:hover {
  background: #fef2f2;
}

.sidebar-task-badge {
  margin-left: auto;
  background: #2563eb;
  color: white;
  font-size: 0.688rem;
  font-weight: 600;
  padding: 0.125rem 0.375rem;
  border-radius: 10px;
  min-width: 18px;
  text-align: center;
}

/* Main wrapper */
.main-wrapper {
  flex: 1;
  margin-left: 240px;
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

/* Top bar */
.top-bar {
  height: 56px;
  background: #ffffff;
  border-bottom: 1px solid #e2e8f0;
  display: flex;
  align-items: center;
  padding: 0 1.5rem;
  position: sticky;
  top: 0;
  z-index: 90;
}

.page-title {
  font-size: 1.25rem;
  font-weight: 700;
  color: #0f172a;
  margin: 0;
}

.top-bar-actions {
  margin-left: auto;
  display: flex;
  align-items: center;
  gap: 0.75rem;
}

.mobile-menu-btn {
  display: none;
  background: none;
  border: none;
  padding: 0.5rem;
  cursor: pointer;
  color: #64748b;
  margin-right: 0.75rem;
}

/* Main content */
.main-content {
  flex: 1;
  padding: 1.5rem 2rem;
  background: #f8fafc;
}

/* Mobile backdrop */
.sidebar-backdrop {
  display: none;
}

/* Mobile responsive */
@media (max-width: 768px) {
  .sidebar {
    transform: translateX(-100%);
    transition: transform 0.25s ease;
  }

  .sidebar.open {
    transform: translateX(0);
  }

  .sidebar-backdrop {
    display: block;
    position: fixed;
    inset: 0;
    background: rgba(0, 0, 0, 0.3);
    z-index: 99;
  }

  .main-wrapper {
    margin-left: 0;
  }

  .mobile-menu-btn {
    display: block;
  }

  .main-content {
    padding: 1rem;
  }
}

/* =====================
   Global Utility Styles
   ===================== */

.page-header {
  margin-bottom: 1.5rem;
}

.page-header h2 {
  font-size: 1.875rem;
  font-weight: 700;
  color: #0f172a;
  margin-bottom: 0.375rem;
  letter-spacing: -0.025em;
}

.page-header p {
  color: #64748b;
  font-size: 0.938rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(280px, 1fr));
  gap: 1.25rem;
  margin-bottom: 1.5rem;
}

.stat-card {
  background: white;
  padding: 1.25rem;
  border-radius: 10px;
  border: 1px solid #e2e8f0;
  transition: all 0.2s ease;
}

.stat-card:hover {
  border-color: #cbd5e1;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.06);
}

.stat-label {
  color: #64748b;
  font-size: 0.875rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  margin-bottom: 0.625rem;
}

.stat-value {
  font-size: 2.25rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.stat-card.warning .stat-value {
  color: #ea580c;
}

.stat-card.success .stat-value {
  color: #059669;
}

.stat-card.danger .stat-value {
  color: #dc2626;
}

.stat-card.info .stat-value {
  color: #2563eb;
}

.card {
  background: white;
  border-radius: 10px;
  padding: 1.25rem;
  border: 1px solid #e2e8f0;
  margin-bottom: 1.25rem;
}

.card-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1rem;
  padding-bottom: 0.875rem;
  border-bottom: 1px solid #e2e8f0;
}

.card-title {
  font-size: 1.125rem;
  font-weight: 700;
  color: #0f172a;
  letter-spacing: -0.025em;
}

.table-container {
  overflow-x: auto;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f8fafc;
  border-top: 1px solid #e2e8f0;
  border-bottom: 1px solid #e2e8f0;
}

th {
  text-align: left;
  padding: 0.5rem 0.75rem;
  font-weight: 600;
  color: #475569;
  font-size: 0.75rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

td {
  padding: 0.5rem 0.75rem;
  border-top: 1px solid #f1f5f9;
  color: #334155;
  font-size: 0.875rem;
}

tbody tr {
  transition: background-color 0.15s ease;
}

tbody tr:hover {
  background: #f8fafc;
}

.badge {
  display: inline-block;
  padding: 0.313rem 0.75rem;
  border-radius: 6px;
  font-size: 0.75rem;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.025em;
}

.badge.success {
  background: #d1fae5;
  color: #065f46;
}

.badge.warning {
  background: #fed7aa;
  color: #92400e;
}

.badge.danger {
  background: #fecaca;
  color: #991b1b;
}

.badge.info {
  background: #dbeafe;
  color: #1e40af;
}

.badge.increasing {
  background: #d1fae5;
  color: #065f46;
}

.badge.decreasing {
  background: #fecaca;
  color: #991b1b;
}

.badge.stable {
  background: #e0e7ff;
  color: #3730a3;
}

.badge.high {
  background: #fecaca;
  color: #991b1b;
}

.badge.medium {
  background: #fed7aa;
  color: #92400e;
}

.badge.low {
  background: #dbeafe;
  color: #1e40af;
}

.loading {
  text-align: center;
  padding: 3rem;
  color: #64748b;
  font-size: 0.938rem;
}

.error {
  background: #fef2f2;
  border: 1px solid #fecaca;
  color: #991b1b;
  padding: 1rem;
  border-radius: 8px;
  margin: 1rem 0;
  font-size: 0.938rem;
}
</style>
