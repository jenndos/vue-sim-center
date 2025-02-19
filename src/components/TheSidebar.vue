<script setup>
import { RouterLink } from 'vue-router';
import IconLogo from './icons/IconLogo.vue';
import IconSchedule from './icons/IconSchedule.vue';
import IconStudySessions from './icons/IconStudySessions.vue';
import IconRooms from './icons/IconRooms.vue';
import IconUsers from './icons/IconUsers.vue';
import IconStudyGroups from './icons/IconStudyGroups.vue';
import IconDevices from './icons/IconDevices.vue';
import IconSystemSettings from './icons/IconSystemSettings.vue';
import IconArchive from './icons/IconArchive.vue';
import IconLogOut from './icons/IconLogOut.vue';
import ArrowLeft from './icons/IconArrowLeft.vue';

import LanguageSelector from '@/components/SidebarSwitcherLang.vue'

import { computed, ref } from "vue";

const links = [
  { to: '/schedule', text: 'Расписание', icon: IconSchedule },
  { to: '/study-sessions', text: 'Учебные сессии', icon: IconStudySessions },
  { to: '/rooms', text: 'Список комнат', icon: IconRooms },
  { to: '/users', text: 'Пользователи', icon: IconUsers },
  { to: '/study-groups', text: 'Учебные группы', icon: IconStudyGroups },
  { to: '/devices', text: 'Список устройств', icon: IconDevices },
  { to: '/system-settings', text: 'Настройки системы', icon: IconSystemSettings },
  { to: '/archive', text: 'Архив', icon: IconArchive },
];

const user = {
  fullName: "Барнаби Мармадюк"
}

const userInitials = computed(() => {
  const words = user.fullName.trim().split(" ");
  return words.length === 2 ? (words[0][0] + words[1][0]).toUpperCase() : "";
});

const isCollapsed = ref(false);

const toggleSidebar = () => {
  isCollapsed.value = !isCollapsed.value;
};

</script>

<template>
  <div class="sidebar" :class="{ 'collapsed': isCollapsed }">
    <button class="icon-toggle" @click="toggleSidebar">
      <ArrowLeft :class="{ 'rotated': isCollapsed }"/>
    </button>

    <div class="logo-container">
      <IconLogo class="logo" />
      <span class="label" v-if="!isCollapsed">Сим Центр</span>
    </div>
    <nav>
      <ul>
        <li v-for="link in links" :key="link.to">
          <RouterLink :to="link.to"
                      class="nav-link"
                      active-class="active-link"
          >
            <component :is="link.icon" v-if="link.icon" class="icon" />
            <span v-if="!isCollapsed">{{ link.text }}</span>
          </RouterLink>
        </li>
      </ul>
    </nav>

    <RouterLink :to="'/profile'"
                class='profile'
                active-class="active-link"
    >
      <div class="profile-header" v-if="!isCollapsed">
        <h4>Барнаби Мармадюк</h4>
        <p>Преподаватель</p>
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
      <small>
        Версия 1.02
      </small>
    </footer>
  </div>
</template>

<style scoped>
  .sidebar {
    position: relative;
    padding: 19px 12px;
    /*height: 100vh;*/
    /*#TODO Change width to variable*/
    background-color: #ffffff;
    flex-basis: 274px;
    transition: all 0.3s ease;
  }

  /* Logo */
  .logo-container {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 19px auto 43px;
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
    background-color: #F4F4F4;
  }

  .active-link,
  .active-link .icon {
    color: #ffffff !important;
    background-color: #3761F3 !important;
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
    font-weight: 800;
    margin-top: 18px;
  }

  .btn-logout .icon {
    margin-right: 12px;
  }

  footer {
    margin-top: 18px;
  }

  footer small {
    color: #797983;
    font-weight: 500;
  }

  .icon-toggle {
    /*#TODO Make with variables*/
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
    flex-basis: 50px; /* Collapsed width */
  }

  /* Hide elements when collapsed */
  .collapsed .label,
  .collapsed .nav-link span,
  .collapsed .profile-header,
  .collapsed footer {
    display: none;
  }

  /* Adjust logo position when collapsed */
  .collapsed .logo-container {
    justify-content: center;
    margin: 19px auto;
  }

  /* Center icons in collapsed state */
  .collapsed .nav-link {
    justify-content: center;
    padding: 12px 0;
  }

  /* Adjust profile section */
  .collapsed .profile {
    justify-content: center;
    padding: 0;
  }

  /* Rotate toggle icon */
  .icon-toggle .rotated {
    transform: rotate(180deg);
  }

  /* Language selector adjustments */
  .collapsed .language-selector {
    display: flex;
    justify-content: center;
  }

  /* Logout button adjustments */
  .collapsed .btn-logout {
    justify-content: center;
  }

  .collapsed svg {
    margin: 0 !important;
  }

  /* Specific adjustments for navigation icons */
  .collapsed .nav-link .icon {
    margin-right: 0;
  }

  /* Adjust logo margins */
  .collapsed .logo {
    margin-right: 0;
  }

  /* Language selector flags */
  .collapsed .language-selector img {
    margin: 0;
  }

  /* Logout button icon */
  .collapsed .btn-logout .icon {
    margin-right: 0;
  }

  /* Toggle button icon */
  .collapsed .icon-toggle svg {
    margin: 0;
  }

</style>
