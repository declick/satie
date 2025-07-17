<template>
  <div class="feuille-perso">
    <h1>Feuille de personnage</h1>
    <form v-if="!valide" @submit.prevent>
      <div class="carac-row">
        <label>Habilité</label>
        <input type="number" v-model="habilete" min="0" inputmode="numeric" />
      </div>
      <div class="carac-row">
        <label>Endurance</label>
        <input type="number" v-model="endurance" min="0" inputmode="numeric" />
      </div>
      <div class="carac-row">
        <label>Chance</label>
        <input type="number" v-model="chance" min="0" inputmode="numeric" />
      </div>
      <div class="carac-row">
        <label>Or</label>
        <input type="number" v-model="or" min="0" inputmode="numeric" />
      </div>
      <div class="carac-row">
        <label>Provisions</label>
        <input type="number" v-model="provisions" min="0" inputmode="numeric" />
      </div>
      <div class="list-block">
        <label>Équipement</label>
        <ul>
          <li v-for="(item, i) in equipement" :key="i">
            <span>{{ item }}</span>
            <button type="button" @click="equipement.splice(i,1)">✕</button>
          </li>
        </ul>
        <input v-model="newEquip" @keyup.enter="addEquip" placeholder="Ajouter un objet..." />
        <button type="button" class="add-btn" @click="addEquip">Ajouter</button>
      </div>
      <button class="valider-btn" @click="validerPerso">Valider</button>
      <p v-if="erreur" class="erreur-message">{{ erreur }}</p>
    </form>
    <div v-else class="recap">
      <h2>Stats du héro</h2>
      <div class="stats-cards">
        <div class="stat-card">
          <span class="stat-icon">🗡️</span>
          <span class="stat-label">Habilité</span>
          <span class="stat-value">{{ habilete }}</span>
        </div>
        <div class="stat-card">
          <span class="stat-icon">❤️</span>
          <span class="stat-label">Endurance</span>
          <span class="stat-value">{{ endurance }}</span>
        </div>
        <div class="stat-card">
          <span class="stat-icon">🍀</span>
          <span class="stat-label">Chance</span>
          <span class="stat-value">{{ chance }}</span>
        </div>
        <div class="stat-card">
          <span class="stat-icon">💰</span>
          <span class="stat-label">Or</span>
          <span class="stat-value">{{ or }}</span>
        </div>
        <div class="stat-card">
          <span class="stat-icon">🥪</span>
          <span class="stat-label">Provisions</span>
          <span class="stat-value">{{ provisions }}</span>
        </div>
      </div>
      <div class="recap-cards">
        <div class="recap-card">
          <span class="recap-icon">🎒</span>
          <span class="recap-label">Équipement</span>
          <ul>
            <li v-for="(item, i) in equipement" :key="i">{{ item }}</li>
            <li v-if="equipement.length === 0"><i>Aucun</i></li>
          </ul>
        </div>
      </div>
      <button class="modifier-btn" @click="modifierPerso">Modifier</button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import Swal from 'sweetalert2'
import { useToast } from "vue-toastification";

const emit = defineEmits(['perso-valide'])
const STORAGE_KEY = 'feuille-perso-v1'
const habilete = ref(0)
const endurance = ref(0)
const chance = ref(0)
const or = ref(0)
const provisions = ref(0)
const equipement = ref([])
const newEquip = ref('')
const valide = ref(false)
const erreur = ref('')

// Charger depuis localStorage au démarrage
onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) {
    try {
      const data = JSON.parse(saved)
      habilete.value = data.habilete ?? 0
      endurance.value = data.endurance ?? 0
      chance.value = data.chance ?? 0
      or.value = data.or ?? 0
      provisions.value = data.provisions ?? 0
      equipement.value = data.equipement ?? []
      valide.value = data.valide ?? false
    } catch {}
  }
})

// Sauvegarder à chaque modification
watch([
  habilete, endurance, chance, or, provisions, equipement, valide
], () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    habilete: habilete.value,
    endurance: endurance.value,
    chance: chance.value,
    or: or.value,
    provisions: provisions.value,
    equipement: equipement.value,
    valide: valide.value
  }))
}, { deep: true })

function addEquip() {
  if (newEquip.value.trim()) {
    equipement.value.push(newEquip.value.trim())
    newEquip.value = ''
  }
}

function validerPerso() {
  const toast = useToast();
  if (endurance.value <= 0) {
    toast.error("L'endurance doit être supérieure à 0 pour valider le personnage.", {
      position: "top-right",
      timeout: 3500
    });
    return;
  }
  erreur.value = ''
  valide.value = true
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    habilete: habilete.value,
    endurance: endurance.value,
    chance: chance.value,
    or: or.value,
    provisions: provisions.value,
    equipement: equipement.value,
    valide: true
  }))
  emit('perso-valide')
}

function modifierPerso() {
  valide.value = false
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    habilete: habilete.value,
    endurance: endurance.value,
    chance: chance.value,
    or: or.value,
    provisions: provisions.value,
    equipement: equipement.value,
    valide: false
  }))
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

<style scoped>
.feuille-perso {
  max-width: 500px;
  margin: 0 auto 1.5rem auto;
  padding: 1.2rem 0.5rem 0.5rem 0.5rem;
}
form {
  display: flex;
  flex-direction: column;
  gap: 1.2rem;
}
.carac-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1.2em;
  background: #f8f8f8;
  border-radius: 10px;
  padding: 0.7em 1em;
  font-size: 1.1em;
}
.carac-row label {
  font-weight: 600;
  color: #222;
}
.carac-row input {
  width: 90px;
  font-size: 1.1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.3em 0.7em;
  background: #fff;
  color: #222;
}
.list-block {
  background: #f8f8f8;
  border-radius: 10px;
  padding: 1em 1em 0.7em 1em;
  margin-bottom: 0.5em;
}
.list-block label {
  font-weight: 600;
  color: #222;
  display: block;
  margin-bottom: 0.5em;
}
.list-block ul {
  list-style: none;
  padding: 0;
  margin: 0 0 0.5em 0;
}
.list-block li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: #e6f7f1;
  border-radius: 6px;
  margin-bottom: 0.3em;
  padding: 0.3em 0.7em;
  font-size: 1em;
  color: #222;
}
.list-block li button {
  background: none;
  border: none;
  color: #e44e6e;
  font-size: 1.2em;
  cursor: pointer;
  margin-left: 0.5em;
}
.list-block input {
  width: 70%;
  font-size: 1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.3em 0.7em;
  background: #fff;
  color: #222;
  margin-bottom: 0.5em;
}
.add-btn {
  background: #42b883;
  color: #fff;
  border: none;
  border-radius: 6px;
  padding: 0.4em 1.2em;
  font-size: 1em;
  font-weight: 600;
  cursor: pointer;
  margin-left: 0.5em;
  margin-bottom: 0.5em;
  transition: background 0.2s;
}
.add-btn:hover {
  background: #2c8c6b;
}
.valider-btn {
  background: #42b883;
  color: #fff;
  border: none;
  border-radius: 8px;
  padding: 0.7em 2em;
  font-size: 1.1em;
  font-weight: 700;
  margin: 1.2em auto 0 auto;
  display: block;
  box-shadow: 0 2px 8px #42b88322;
  transition: background 0.2s;
}
.valider-btn:hover {
  background: #2c8c6b;
}
.recap {
  background: #f8f8f8;
  color: #222;
  border-radius: 12px;
  padding: 1.5em 1em 1em 1em;
  margin-top: 1.2em;
  box-shadow: 0 2px 8px #0001;
}
.recap-row {
  margin-bottom: 1em;
  font-size: 1.1em;
}
.modifier-btn {
  background: #aaa;
  color: #222;
  border: none;
  border-radius: 8px;
  padding: 0.6em 1.5em;
  font-size: 1em;
  font-weight: 600;
  margin: 1.2em auto 0 auto;
  display: block;
  box-shadow: 0 2px 8px #aaa2;
  transition: background 0.2s;
}
.modifier-btn:hover {
  background: #888;
}
.stats-cards {
  display: flex;
  flex-wrap: wrap;
  gap: 1.1em;
  justify-content: center;
  margin-bottom: 1.5em;
}
.stat-card {
  background: linear-gradient(135deg, #e6f7f1 60%, #f8f8f8 100%);
  box-shadow: 0 2px 8px #42b88322, 0 1px 2px #0001;
  border-radius: 14px;
  padding: 1em 1.1em 0.7em 1.1em;
  min-width: 90px;
  min-height: 80px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: 1.1em;
  position: relative;
  transition: transform 0.15s;
}
.stat-card:hover {
  transform: scale(1.04) translateY(-2px);
  box-shadow: 0 4px 16px #42b88333;
}
.stat-icon {
  font-size: 1.7em;
  margin-bottom: 0.2em;
  filter: drop-shadow(0 1px 2px #42b88333);
}
.stat-label {
  font-size: 0.95em;
  color: #42b883;
  font-weight: 600;
  margin-bottom: 0.1em;
}
.stat-value {
  font-size: 1.3em;
  font-weight: 700;
  color: #222;
}
.recap-cards {
  display: flex;
  flex-direction: column;
  gap: 1.1em;
  margin-bottom: 1.5em;
}
.recap-card {
  background: linear-gradient(135deg, #e6f7f1 60%, #f8f8f8 100%);
  box-shadow: 0 2px 8px #42b88322, 0 1px 2px #0001;
  border-radius: 14px;
  padding: 1em 1.1em 0.7em 1.1em;
  display: flex;
  flex-direction: column;
  align-items: flex-start;
  font-size: 1.05em;
  color: #222;
}
.recap-icon {
  font-size: 1.3em;
  margin-bottom: 0.1em;
  filter: drop-shadow(0 1px 2px #42b88333);
}
.recap-label {
  font-size: 1em;
  color: #42b883;
  font-weight: 600;
  margin-bottom: 0.2em;
  margin-left: 0.2em;
}
.recap-card ul {
  list-style: disc inside;
  margin: 0.2em 0 0 0.5em;
  padding: 0;
}
.recap-card li {
  margin-bottom: 0.2em;
  font-size: 0.98em;
}
.erreur-message {
  color: #e44e6e;
  font-weight: bold;
  margin-top: 0.7em;
  text-align: center;
}
@media (max-width: 500px) {
  .feuille-perso {
    padding: 0.5rem 0.1rem 0.5rem 0.1rem;
  }
  .carac-row input, .list-block input {
    width: 60px;
    font-size: 0.98em;
  }
  .stats-cards {
    gap: 0.6em;
  }
  .stat-card {
    min-width: 70px;
    min-height: 65px;
    padding: 0.7em 0.5em 0.5em 0.5em;
    font-size: 0.98em;
  }
  .stat-icon {
    font-size: 1.2em;
  }
  .stat-label {
    font-size: 0.8em;
  }
  .stat-value {
    font-size: 1.1em;
  }
  .recap-cards {
    gap: 0.6em;
  }
  .recap-card {
    font-size: 0.97em;
    padding: 0.7em 0.5em 0.5em 0.5em;
  }
  .recap-icon {
    font-size: 1em;
  }
  .recap-label {
    font-size: 0.85em;
  }
}
</style> 