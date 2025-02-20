<script setup>
import { ref } from 'vue'
import IconArrowDown from '@/components/icons/IconSidebarArrowDown.vue'

defineProps({
  collapsed: Boolean,
})

const languages = [
  { code: 'en', label: 'English', flag: 'us' },
  { code: 'ru', label: 'Русский', flag: 'ru' },
  { code: 'es', label: 'Español', flag: 'es' },
  { code: 'fr', label: 'Français', flag: 'fr' },
  { code: 'de', label: 'Deutsch', flag: 'de' },
]

const selectedLanguage = ref(languages.find((lang) => lang.code === 'ru'))
const showDropdown = ref(false)

const selectLanguage = (language) => {
  selectedLanguage.value = language
  showDropdown.value = false
}
</script>

<template>
  <div class="language-selector">
    <div class="dropdown-header" @click="showDropdown = !showDropdown">
      <div class="selected-option">
        <img
          :src="`https://flagcdn.com/w320/${selectedLanguage.flag}.png`"
          width="24"
          height="24"
          :alt="selectedLanguage.label"
        />
        <span v-if="!collapsed">{{ selectedLanguage.label }}</span>
      </div>
      <span v-if="!collapsed" :class="{ rotated: showDropdown }" class="dropdown-arrow">
        <IconArrowDown />
      </span>
    </div>

    <ul v-if="showDropdown" class="dropdown-menu">
      <li v-for="language in languages" :key="language.code" @click="selectLanguage(language)">
        <img
          :src="`https://flagcdn.com/w320/${language.flag}.png`"
          width="24"
          height="24"
          :alt="language.label"
        />
        <span v-if="!collapsed">{{ language.label }}</span>
      </li>
    </ul>
  </div>
</template>

<style scoped>
.language-selector {
  position: relative;
  width: 100%;
  font-family: Manrope, sans-serif;
}

.dropdown-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px;
  border: 1px solid rgba(224, 224, 224, 1);
  border-radius: 8px;
  background-color: white;
  font-size: 16px;
  cursor: pointer;
  user-select: none;
  transition: padding 0.3s ease;
}

.dropdown-header .selected-option span {
  font-weight: 800;
}

.language-selector.collapsed .dropdown-header {
  padding: 8px;
  justify-content: center;
}

.dropdown-arrow {
  display: flex;
  justify-content: center;
  align-items: center;
  transition: transform 0.3s ease;
}

.rotated {
  transform: rotate(180deg);
}

.selected-option {
  display: flex;
  align-items: center;
  gap: 12px;
}

.dropdown-menu {
  position: absolute;
  bottom: calc(100% + 4px);
  left: 0;
  right: 0;
  margin: 0;
  padding: 0;
  list-style: none;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  background-color: white;
  z-index: 10;
  box-shadow: 0 2px 6px rgba(0, 0, 0, 0.15);
}

.dropdown-menu li {
  padding: 12px;
  display: flex;
  align-items: center;
  gap: 8px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.language-selector.collapsed .dropdown-menu li {
  justify-content: center;
}

.dropdown-menu li:hover {
  background-color: #f5f5f5;
}

img {
  display: block;
  width: 24px;
  height: 24px;
  border-radius: 4px;
}

.language-selector.collapsed span {
  display: none;
}
</style>
