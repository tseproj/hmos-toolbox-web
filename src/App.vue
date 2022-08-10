<template>
  <n-notification-provider placement="top">
    <div class="app">
      <Page />
    </div>
  </n-notification-provider>
</template>

<script setup lang="ts">
import Page from './components/Page.vue'
</script>

<style scoped>
.app {
  margin: 1rem;
  user-select: none;
}
</style>