<script setup>
import { ref, computed } from 'vue'
import SidebarMenu from './components/SidebarMenu.vue'
import Home from './pages/Home.vue'
import Perso from './pages/Perso.vue'
import Combat from './pages/Combat.vue'
import ChancePage from './pages/ChancePage.vue'
import Rules from './pages/Rules.vue'

const currentPage = ref('home')
const adventureStarted = ref(false)
const persoVersion = ref(0)

// Persistance : lire l'état au chargement
if (localStorage.getItem('adventureStarted') === 'true') {
  adventureStarted.value = true
}

const hasPerso = computed(() => {
  persoVersion.value // dépendance forcée
  const data = localStorage.getItem('feuille-perso-v1')
  if (!data) return false
  try {
    const obj = JSON.parse(data)
    // Perso considéré comme créé si fiche validée ET au moins une stat > 0
    return obj.valide === true && (
      obj.endurance > 0 ||
      obj.habilete > 0 ||
      obj.chance > 0
    )
  } catch {
    return false
  }
})

function handleNavigate(page) {
  if (page === 'perso') {
    // On ne supprime plus la fiche perso ici !
    adventureStarted.value = true;
    localStorage.setItem('adventureStarted', 'true');
    // persoVersion.value++; // Pas besoin ici
  }
  currentPage.value = page;
}

function resetAdventure() {
  adventureStarted.value = false;
  localStorage.removeItem('adventureStarted');
  localStorage.removeItem('feuille-perso-v1'); // fiche perso
  localStorage.removeItem('ennemi-v1');        // fiche ennemi
  // Ajoute ici toute autre clé liée à la partie si besoin
  currentPage.value = 'home';
  persoVersion.value++; // Force la recomputation de hasPerso
}
</script>

<template>
  <div class="app-layout">
    <SidebarMenu :current="currentPage" :adventure-started="adventureStarted" :has-perso="hasPerso" @navigate="handleNavigate" />
    <main class="main-content">
      <Home v-if="currentPage === 'home'" @navigate="handleNavigate" :adventure-started="adventureStarted" />
      <Perso v-else-if="currentPage === 'perso'" @perso-valide="persoVersion++" />
      <Combat v-else-if="currentPage === 'ennemis'" @player-dead="resetAdventure" />
      <ChancePage v-else-if="currentPage === 'chance'" />
      <Rules v-else-if="currentPage === 'regle'" />
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
  padding: 1.2rem 1rem 90px 1rem;
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
