<script setup>
import { RouterView } from 'vue-router'
import TheSidebar from './components/TheSidebar.vue'
</script>

<template>
  <div class="layout">
    <TheSidebar />
    <div class="content">
      <RouterView />
    </div>
  </div>
</template>

<style scoped>
.layout {
  display: flex;
  background-color: #f4f3f8;
}

.content {
  flex-grow: 1;
  margin: 8px;
  padding: 24px;
  background-color: #fff;
  border-radius: 12px;
}
</style>
