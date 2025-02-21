<template>
  <div class="search-study-sessions">
    <div class="search-container">
      <div class="search-input-wrapper">
        <IconSearch class="icon-search" />
        <input
          v-model="searchQuery"
          type="text"
          class="search-input"
          placeholder="Поиск"
          @input="onSearch"
        />
      </div>
      <button class="icon-button" @click="$emit('filter')">
        <IconSearchFilter />
      </button>
      <button class="icon-button" @click="$emit('sort')">
        <IconSearchSort />
      </button>
    </div>
  </div>
</template>

<script>
import IconSearchFilter from '@/components/icons/IconSearchFilter.vue'
import IconSearchSort from '@/components/icons/IconSearchSort.vue'
import IconSearch from '@/components/icons/IconSearch.vue'

export default {
  components: { IconSearch, IconSearchSort, IconSearchFilter },
  data() {
    return {
      searchQuery: '',
    }
  },
  methods: {
    onSearch() {
      this.$emit('search', this.searchQuery.trim()) // Add trim()
    },
  },
  watch: {
    searchQuery(newVal) {
      if (newVal === '') {
        this.$emit('search', '') // Explicitly emit empty string
      }
    },
  },
}
</script>

<style scoped>
.search-study-sessions {
  display: flex;
  align-items: center;
}
.search-container {
  display: flex;
  gap: 12px;
  align-items: center;
}
.search-input-wrapper {
  position: relative;
  flex-grow: 1;
}
.icon-search {
  position: absolute;
  left: 16px;
  top: 50%;
  transform: translateY(-50%);
  pointer-events: none;
}
.search-input {
  width: 260px;
  padding: 12px 16px 12px 48px;
  border: 1px solid #d0d5dd;
  border-radius: 12px;
  font-size: 16px;
  color: #667085;
  background-color: white;
}
.search-input:focus {
  outline: none;
  border-color: #84caff;
  box-shadow: 0 0 0 2px #d1e9ff;
}
.icon-button {
  width: 44px;
  height: 44px;
  display: flex;
  align-items: center;
  justify-content: center;
  border: 1px solid #d0d5dd;
  border-radius: 8px;
  background-color: white;
  cursor: pointer;
  transition: background-color 0.2s;
}
.icon-button:hover {
  background-color: #f9fafb;
}
</style>
