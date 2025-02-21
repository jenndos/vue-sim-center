<template>
  <div class="table-study-sessions">
    <table>
      <thead>
        <tr>
          <th @click="sortBy('datetime', 'desc')" class="th-center">
            Дата и время
            <span v-if="sortKey === 'datetime'" class="icon-wrapper">
              <IconTableFilterArrow
                class="icon-table-filter"
                :class="{ rotated: sortOrder !== 'asc' }"
              />
            </span>
          </th>
          <th @click="sortBy('status', 'asc', 'datetime')">
            Статус
            <span v-if="sortKey === 'status'">▼</span>
          </th>
          <th @click="sortBy('module', 'asc')">
            Название учебного модуля
            <span v-if="sortKey === 'module'">
              <span v-if="sortOrder === 'asc'">▲</span>
              <span v-else>▼</span>
            </span>
          </th>
          <th @click="sortBy('type', 'asc', 'datetime')">
            Тип сессии
            <span v-if="sortKey === 'type'">
              <span v-if="sortOrder === 'asc'">▼</span>
              <span v-else>▼</span>
            </span>
          </th>
          <th @click="sortBy('room', 'asc')">
            Комната
            <span v-if="sortKey === 'room'">
              <span v-if="sortOrder === 'asc'">▲</span>
              <span v-else>▼</span>
            </span>
          </th>
          <th @click="sortBy('group', 'asc')">
            Группа
            <span v-if="sortKey === 'group'">
              <span v-if="sortOrder === 'asc'">▲</span>
              <span v-else>▼</span>
            </span>
          </th>
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
          <td>
            {{
              session.rooms
                .map((room) => room.id)
                .sort((a, b) => a - b)
                .map((id) => `Комната ${id}`)
                .join(', ')
            }}
          </td>
          <td>
            {{ session.groups.map((group) => group.name).join(', ') }}
          </td>
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
import IconTableFilterArrow from '@/components/icons/IconTableFilterArrow.vue'

export default {
  components: { IconTableFilterArrow, IconPaginationArrowLeft },
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
    // Remove duplicate sessions based on their id.
    uniqueSessions() {
      const map = new Map()
      this.sessions.forEach((session) => {
        if (!map.has(session.id)) {
          map.set(session.id, session)
        }
      })
      return Array.from(map.values())
    },
    sortedSessions() {
      const sorted = [...this.uniqueSessions]
      const orderFactor = this.sortOrder === 'asc' ? 1 : -1
      const statusOrder = { planned: 1, canceled: 2, completed: 3 }
      const typeOrder = { accreditation: 1, lesson: 2, examination: 3 }
      const extractNumber = (str) => {
        const match = str.match(/\d+/)
        return match ? parseInt(match[0], 10) : Infinity
      }

      sorted.sort((a, b) => {
        let result = 0
        switch (this.sortKey) {
          case 'datetime': {
            const aDate = new Date(a.start)
            const bDate = new Date(b.start)
            result = aDate - bDate
            break
          }
          case 'status': {
            const aVal = statusOrder[a.status.name] || 99
            const bVal = statusOrder[b.status.name] || 99
            result = aVal - bVal
            break
          }
          case 'module': {
            result = a.module.localeCompare(b.module)
            break
          }
          case 'type': {
            const aVal = typeOrder[a.type.name] || 99
            const bVal = typeOrder[b.type.name] || 99
            result = aVal - bVal
            break
          }
          case 'room': {
            const aMin = Math.min(...a.rooms.map((room) => room.id))
            const bMin = Math.min(...b.rooms.map((room) => room.id))
            result = aMin - bMin
            break
          }
          case 'group': {
            const aMin = Math.min(...a.groups.map((g) => extractNumber(g.name)))
            const bMin = Math.min(...b.groups.map((g) => extractNumber(g.name)))
            result = aMin - bMin
            break
          }
        }
        return result * orderFactor
      })
      return sorted
    },
    paginatedSessions() {
      const start = (this.currentPage - 1) * this.itemsPerPage
      return this.sortedSessions.slice(start, start + this.itemsPerPage)
    },
    totalPages() {
      return Math.ceil(this.sortedSessions.length / this.itemsPerPage)
    },
  },
  methods: {
    sortBy(key, defaultOrder, resetKey) {
      if (this.sortKey === key) {
        if (resetKey) {
          this.sortKey = resetKey
          this.sortOrder = 'desc'
        } else {
          this.sortOrder = this.sortOrder === 'asc' ? 'desc' : 'asc'
        }
      } else {
        this.sortKey = key
        this.sortOrder = defaultOrder
      }
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
      const statuses = {
        planned: 'Запланировано',
        canceled: 'Отменено',
        completed: 'Завершено',
      }
      return statuses[status] || status
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
      const names = {
        accreditation: 'Аккредитация',
        lesson: 'Урок',
        examination: 'Экзамен',
      }
      return names[sessionName] || sessionName
    },
    getSessionName(sessionName) {
      return {
        'session-cell': true,
        'session-accreditation': sessionName === 'accreditation',
        'session-lesson': sessionName === 'lesson',
        'session-examination': sessionName === 'examination',
      }
    },
    resetPage() {
      this.currentPage = 1
    },
  },
  watch: {
    sessions: {
      handler() {
        this.resetPage()
      },
      immediate: true,
    },
    sortKey: 'resetPage',
    sortOrder: 'resetPage',
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
.icon-table-filter {
  margin-left: 18px;
}
.th-center {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 4px;
}
.icon-wrapper {
  display: inline-flex;
  align-items: center;
}
</style>
