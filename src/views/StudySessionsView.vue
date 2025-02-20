<template>
  <div class="study-sessions">
    <div class="header-wrapper">
      <h1>Учебные сессии</h1>
      <div class="search-create-btn-wrapper">
        <SearchStudySessions @search="handleSearch" />
        <ButtonCreateStudySessions />
      </div>
    </div>
    <TableStudySessions :sessions="filteredSessions" class="table-study-sessions" />
  </div>
</template>

<script>
import SearchStudySessions from '@/components/SearchStudySessions.vue'
import TableStudySessions from '@/components/TableStudySessions.vue'
import data from '@/data/data.json'
import ButtonCreateStudySessions from '@/components/ButtonCreateStudySessions.vue'

export default {
  components: {
    ButtonCreateStudySessions,
    SearchStudySessions,
    TableStudySessions,
  },
  data() {
    return {
      sessions: data.sessions,
      searchQuery: '',
    }
  },
  computed: {
    filteredSessions() {
      if (!this.searchQuery) return this.sessions

      const searchTerms = this.searchQuery.toLowerCase().split(' ')

      return this.sessions.filter((session) =>
        searchTerms.every((term) => session.module.toLowerCase().includes(term)),
      )
    },
  },
  methods: {
    handleSearch(query) {
      this.searchQuery = query
    },
  },
}
</script>

<style scoped>
.header-wrapper {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.search-create-btn-wrapper {
  display: flex;
}
</style>
