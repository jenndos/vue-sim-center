<template>
  <div class="table-study-sessions">
    <table>
      <thead>
        <tr>
          <th @click="sortByDateTime">Дата и время</th>
          <th @click="sortByStatus">Статус</th>
          <th @click="sortByModuleName">Название учебного модуля</th>
          <th @click="sortBySessionType">Тип сессии</th>
          <th @click="sortByRoom">Комната</th>
          <th @click="sortByGroup">Группа</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="session in paginatedSessions" :key="session.id">
          <td>{{ formatDateTime(session.start, session.end) }}</td>
          <td>
            <span :class="getStatusClass(session.status.name)">
              {{ formatStatus(session.status.name) }}
            </span>
          </td>
          <td>{{ session.module }}</td>
          <td>
            <span :class="getSessionName(session.type.name)">
              {{ formatSessionName(session.type.name) }}
            </span>
          </td>
          <td>{{ session.rooms.map((room) => room.id).join(', ') }}</td>
          <td>{{ session.groups.map((group) => group.name).join(', ') }}</td>
        </tr>
      </tbody>
      <tfoot class="table-foot">
        <tr>
          <td colspan="6">
            <div class="pagination">
              <button @click="prevPage" :disabled="currentPage === 1">
                <IconPaginationArrowLeft class="icon-pagination-arrow" />
              </button>
              <button
                v-for="page in totalPages"
                :key="page"
                @click="currentPage = page"
                :class="{ active: currentPage === page }"
              >
                {{ page }}
              </button>
              <button @click="nextPage" :disabled="currentPage === totalPages">
                <IconPaginationArrowLeft class="icon-pagination-arrow rotated" />
              </button>
            </div>
          </td>
        </tr>
      </tfoot>
    </table>
  </div>
</template>

<script>
import IconPaginationArrowLeft from '@/components/icons/IconPaginationArrowLeft.vue'

export default {
  components: { IconPaginationArrowLeft },
  props: {
    sessions: {
      type: Array,
      required: true,
    },
  },
  data() {
    return {
      currentPage: 1,
      itemsPerPage: 50,
      sortKey: 'datetime',
      sortOrder: 'desc',
    }
  },
  computed: {
    sortedSessions() {
      let sorted = [...this.sessions]

      switch (this.sortKey) {
        case 'datetime':
          sorted.sort((a, b) => {
            return this.sortOrder === 'asc'
              ? new Date(a.start) - new Date(b.start)
              : new Date(b.start) - new Date(a.start)
          })
          break
        case 'status':
          sorted.sort((a, b) => a.status.name.localeCompare(b.status.name))
          break
        case 'module':
          sorted.sort((a, b) => a.module.localeCompare(b.module))
          break
        case 'type':
          sorted.sort((a, b) => a.type.name.localeCompare(b.type.name))
          break
        case 'room':
          sorted.sort(
            (a, b) =>
              Math.min(...a.rooms.map((room) => room.id)) -
              Math.min(...b.rooms.map((room) => room.id)),
          )
          break
        case 'group':
          sorted.sort((a, b) => a.groups[0].name.localeCompare(b.groups[0].name))
          break
      }

      return sorted
    },
    paginatedSessions() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      const end = start + this.itemsPerPage
      return this.sortedSessions.slice(start, end)
    },
    totalPages() {
      return Math.ceil(this.sortedSessions.length / this.itemsPerPage)
    },
  },
  methods: {
    sortByDateTime() {
      this.sortKey = 'datetime'
      this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc'
    },
    sortByStatus() {
      this.sortKey = 'status'
    },
    sortByModuleName() {
      this.sortKey = 'module'
    },
    sortBySessionType() {
      this.sortKey = 'type'
    },
    sortByRoom() {
      this.sortKey = 'room'
    },
    sortByGroup() {
      this.sortKey = 'group'
    },
    prevPage() {
      if (this.currentPage > 1) this.currentPage--
    },
    nextPage() {
      if (this.currentPage < this.totalPages) this.currentPage++
    },
    formatDateTime(start, end) {
      const startDate = new Date(start)
      const endDate = new Date(end)
      return `${startDate.toLocaleDateString()}, ${startDate.toLocaleTimeString()} - ${endDate.toLocaleTimeString()}`
    },
    formatStatus(status) {
      switch (status) {
        case 'planned':
          return 'Запланировано'
        case 'canceled':
          return 'Отменено'
        case 'completed':
          return 'Завершено'
        default:
          return status
      }
    },
    getStatusClass(status) {
      return {
        'status-cell': true,
        'status-planned': status === 'planned',
        'status-canceled': status === 'canceled',
        'status-completed': status === 'completed',
      }
    },
    formatSessionName(sessionName) {
      switch (sessionName) {
        case 'accreditation':
          return 'Аккредитация'
        case 'lesson':
          return 'Урок'
        case 'examination':
          return 'Экзамен'
        default:
          return sessionName
      }
    },
    getSessionName(sessionName) {
      return {
        'session-cell': true,
        'session-accreditation': sessionName === 'accreditation',
        'session-lesson': sessionName === 'lesson',
        'session-examination': sessionName === 'examination',
      }
    },
  },
}
</script>

<style scoped>
.table-study-sessions {
  max-width: 100%;
  margin: 20px auto;
  font-family: 'Manrope', sans-serif;
}

table {
  width: 100%;
  border-collapse: separate;
  border-spacing: 0;
  border-width: 1px;
  /*margin-bottom: 10px;*/
  border-radius: 12px;
  overflow: hidden;
  border-color: #e9eaec;
}

tbody tr:nth-child(even) {
  background-color: rgba(244, 244, 244, 1);
}

th {
  background-color: #f2f2f2;
  cursor: pointer;
  user-select: none;
  font-weight: 800;
  border: 1px solid #ddd;
  padding: 8px;
}

th:first-child,
td:first-child {
  white-space: nowrap;
}

td {
  border: 1px solid #ddd;
  padding: 10px 16px;
}

.table-foot {
  background-color: #f5f7f9;
  border-radius: 0 0 12px 12px;
}

.pagination {
  display: flex;
  align-items: center;
  justify-content: center;
  gap: 10px;
}

.pagination button {
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: 500;
  width: 30px;
  border: none;
  height: 30px;
  cursor: pointer;
  background-color: #ffffff;
  border-radius: 8px;
}

.pagination button.active {
  box-shadow: inset 0 0 0 1px rgba(55, 97, 243, 1);
  padding: 10px;
  color: rgba(55, 97, 243, 1);
  background-color: white;
}

.pagination button:hover {
  background-color: rgba(224, 224, 224, 1);
}

button:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}

.status-cell {
  display: inline-block;
  padding: 4px 12px;
  border-radius: 43px;
  text-align: center;
  vertical-align: middle;
}

.status-planned {
  background-color: rgba(175, 191, 245, 1);
}

.status-canceled {
  background-color: #ffabab;
}

.status-completed {
  background-color: rgba(145, 200, 147, 1);
}

.rotated {
  transform: rotate(180deg);
}

.icon-pagination-arrow {
  color: rgba(153, 153, 153, 1);
}
</style>
