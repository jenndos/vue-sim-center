<script setup>
import { RouterLink } from 'vue-router'
import IconLogo from './icons/IconSidebarLogo.vue'
import IconSchedule from './icons/IconSidebarSchedule.vue'
import IconStudySessions from './icons/IconSidebarStudySessions.vue'
import IconRooms from './icons/IconSidebarRooms.vue'
import IconUsers from './icons/IconSidebarUsers.vue'
import IconStudyGroups from './icons/IconSidebarStudyGroups.vue'
import IconDevices from './icons/IconSidebarDevices.vue'
import IconSystemSettings from './icons/IconSidebarSystemSettings.vue'
import IconArchive from './icons/IconSidebarArchive.vue'
import IconLogOut from './icons/IconSidebarLogOut.vue'
import IconArrowLeft from './icons/IconSidebarArrowLeft.vue'

import LanguageSelector from '@/components/SidebarSwitcherLang.vue'

import { computed, ref } from 'vue'

const links = [
  { to: '/schedule', text: 'Расписание', icon: IconSchedule },
  { to: '/study-sessions', text: 'Учебные сессии', icon: IconStudySessions },
  { to: '/rooms', text: 'Список комнат', icon: IconRooms },
  { to: '/users', text: 'Пользователи', icon: IconUsers },
  { to: '/study-groups', text: 'Учебные группы', icon: IconStudyGroups },
  { to: '/devices', text: 'Список устройств', icon: IconDevices },
  { to: '/system-settings', text: 'Настройки системы', icon: IconSystemSettings },
  { to: '/archive', text: 'Архив', icon: IconArchive },
]

const user = {
  fullName: 'Барнаби Мармадюк',
  role: 'Преподаватель'
}

const userInitials = computed(() => {
  const words = user.fullName.trim().split(' ')
  return words.length === 2 ? (words[0][0] + words[1][0]).toUpperCase() : ''
})

const isCollapsed = ref(false)

const toggleSidebar = () => {
  isCollapsed.value = !isCollapsed.value
}
</script>

<template>
  <div class="sidebar" :class="{ collapsed: isCollapsed }">
    <button class="icon-toggle" @click="toggleSidebar">
      <IconArrowLeft :class="{ rotated: isCollapsed }" style="color: white" />
    </button>

    <RouterLink :to="'/'" class="logo-container">
      <IconLogo class="logo" />
      <span class="label" v-if="!isCollapsed">Сим Центр</span>
    </RouterLink>
    <nav>
      <ul>
        <li v-for="link in links" :key="link.to">
          <RouterLink :to="link.to" class="nav-link" active-class="active-link">
            <component :is="link.icon" v-if="link.icon" class="icon" />
            <span v-if="!isCollapsed">{{ link.text }}</span>
          </RouterLink>
        </li>
      </ul>
    </nav>

    <div class="user-menu-wrapper">
      <RouterLink :to="'/profile'" class="profile" active-class="active-link">
        <div class="profile-header" v-if="!isCollapsed">
          <h4>{{ user.fullName }}</h4>
          <p>{{ user.role }}</p>
        </div>
        <div class="profile-logo">
          {{ userInitials }}
        </div>
      </RouterLink>
      <button class="btn-logout">
        <IconLogOut class="icon" />
        <span v-if="!isCollapsed">Выйти</span>
      </button>
      <LanguageSelector :collapsed="isCollapsed" />
      <footer v-if="!isCollapsed">
        <small> Версия 1.02 </small>
      </footer>
    </div>
  </div>
</template>

<style scoped>
.sidebar {
  display: grid;
  grid-template-rows: auto 1fr auto;
  height: 100vh;
  position: relative;
  padding: 19px 12px;
  background-color: #ffffff;
  min-width: 274px;
  transition: all 0.3s ease;
}

.user-menu-wrapper {
  align-self: end;
}

/* Logo */
.logo-container {
  display: flex;
  align-items: center;
  justify-content: center;
  margin: 19px auto 43px;
}

.logo-container span {
  color: #000000;
}

.logo {
  margin-right: 14px;
}

.label {
  font-size: 24px;
  font-weight: 800;
}

/* Links */
.nav-link {
  font-weight: 800;
  margin: 4px 0;
  border-radius: 16px;
  color: #000000;
  display: flex;
  align-items: center;
  padding: 12px;
  text-decoration: none;
}

.nav-link .icon {
  margin-right: 12px;
  color: #999999;
}

.nav-link:not(.active-link):hover {
  cursor: pointer;
  background-color: #f4f4f4;
}

.active-link,
.active-link .icon {
  color: #ffffff !important;
  background-color: #3761f3 !important;
}

/* Profile  */

.profile {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  box-shadow: 0px 4px 24px 0px rgba(0, 0, 0, 0.12);
  border-radius: 16px;
}

.profile h4 {
  color: #000000;
  font-size: 15px;
  font-weight: 800;
}

.profile p {
  color: #797985;
  font-size: 13px;
  font-weight: 500;
}

.profile.active-link h4,
.profile.active-link p {
  color: #ffffff;
}

.profile-logo {
  font-weight: 800;
  color: #ffffff;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 48px;
  height: 48px;
  background-color: rgba(43, 78, 197, 1);
  border-radius: 48px;
}

.btn-logout {
  display: flex;
  justify-content: start;
  align-items: center;
  padding: 12px;
  width: 100%;
  background: none;
  border: none;
  cursor: pointer;
  margin-top: 18px;
  gap: 12px;
}

.btn-logout span {
  font-weight: 800;
}

footer {
  margin-top: 18px;
}

footer small {
  color: #797983;
  font-weight: 500;
}

.icon-toggle {
  position: absolute;
  top: 50px;
  right: -12px;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 24px;
  height: 24px;
  background-color: rgba(47, 49, 68, 1);
  border-radius: 50%;
  border: none;
}

.icon-toggle:hover {
  cursor: pointer;
}

/* Collapsed  */

.sidebar.collapsed {
  min-width: 74px;
}

.collapsed .label,
.collapsed .nav-link span,
.collapsed .profile-header,
.collapsed footer {
  display: none;
}

.collapsed .logo-container {
  justify-content: center;
  margin: 19px auto;
}

.collapsed .nav-link {
  justify-content: center;
  padding: 12px 0;
}

.collapsed .profile {
  justify-content: center;
  padding: 0;
}

.icon-toggle .rotated {
  transform: rotate(180deg);
}

.collapsed .language-selector {
  display: flex;
  justify-content: center;
}

.collapsed .btn-logout {
  justify-content: center;
}

.collapsed svg {
  margin: 0 !important;
}

.collapsed .nav-link .icon {
  margin-right: 0;
}

.collapsed .logo {
  margin-right: 0;
}

.collapsed .language-selector img {
  margin: 0;
}

.collapsed .btn-logout .icon {
  margin-right: 0;
}

.collapsed .icon-toggle svg {
  margin: 0;
}
</style>
