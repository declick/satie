<script setup>
import { ref } from 'vue'
import SidebarMenu from './components/SidebarMenu.vue'
import Home from './pages/Home.vue'
import Perso from './pages/Perso.vue'
import Combat from './pages/Combat.vue'

const currentPage = ref('home')
</script>

<template>
  <div class="app-layout">
    <SidebarMenu :current="currentPage" @navigate="currentPage = $event" />
    <main class="main-content">
      <Home v-if="currentPage === 'home'" />
      <Perso v-else-if="currentPage === 'perso'" />
      <Combat v-else-if="currentPage === 'ennemis'" />
    </main>
  </div>
</template>

<style scoped>
.app-layout {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.main-content {
  flex: 1;
  padding: 1.2rem 1rem 90px 1rem; /* padding-bottom pour le menu */
  box-sizing: border-box;
  width: 100vw;
  max-width: 600px;
  margin: 0 auto;
}
@media (min-width: 600px) {
  .main-content {
    padding: 2rem 2rem 90px 2rem;
    max-width: 700px;
  }
}
</style>
