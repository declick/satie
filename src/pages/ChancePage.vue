<template>
  <div class="chance-page">
    <h1>Chance du héros</h1>
    <div class="chance-card">
      <span class="chance-icon">🍀</span>
      <span class="chance-label">Chance actuelle :</span>
      <span class="chance-value">{{ chance }}</span>
      <span class="chance-label" style="font-size:0.95em; color:#888; margin-top:0.2em;">(Valeur de départ : {{ chanceInitiale }})</span>
    </div>
    <div class="chance-action">
      <div class="bonus-malus-row">
        <label>Bonus/Malus :</label>
        <input type="number" v-model.number="bonusMalus" inputmode="numeric" class="bonus-malus-input" placeholder="+2 ou -1" />
      </div>
      <div class="de-row">
        <Dice :faces="6" v-model="de1" :rolling="rolling" :clickable="false" @roll-end="onRollEnd" />
        <Dice :faces="6" v-model="de2" :rolling="rolling" :clickable="false" />
      </div>
      <button class="chance-btn" @click="lancerChance" :disabled="chance <= 0 || rolling">Tenter sa chance</button>
      <div v-if="result !== null" class="chance-result">
        Résultat des dés : <b>{{ result }}</b>
        <span v-if="bonusMalus">({{ result - bonusMalus >= 0 ? result + (bonusMalus >= 0 ? ' - ' : ' + ') + Math.abs(bonusMalus) : result + (bonusMalus >= 0 ? ' - ' : ' + ') + Math.abs(bonusMalus) }})</span>
        <br />Total : <b>{{ result + (Number(bonusMalus) || 0) }}</b>
        <br />Test de chance :
        <span v-if="result + (Number(bonusMalus) || 0) < chanceInitiale">Raté !</span> <!-- Modifié ici -->
        <span v-else>Réussi !</span>
      </div>
      <div v-if="chance <= 0" class="chance-zero">Tu n'as plus de chance !</div>
    </div>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import Dice from '../components/Dice.vue'
const chance = ref(0)
const chanceInitiale = ref(0)
const STORAGE_KEY = 'feuille-perso-v1'
const de1 = ref(1)
const de2 = ref(1)
const rolling = ref(false)
const result = ref(null)
const bonusMalus = ref(0)

onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) {
    try {
      const data = JSON.parse(saved)
      chance.value = data.chance ?? 0
      chanceInitiale.value = data.chanceInitiale ?? data.chance ?? 0
    } catch {}
  }
})

function lancerChance() {
  if (chance.value <= 0) return
  result.value = null
  rolling.value = true
  de1.value = Math.floor(Math.random() * 6) + 1 // force reroll
  de2.value = Math.floor(Math.random() * 6) + 1
}
function onRollEnd() {
  setTimeout(() => {
    result.value = de1.value + de2.value
    rolling.value = false

    const total = result.value + (Number(bonusMalus.value) || 0)

    // Test de chance : diminuer la chance uniquement en cas d'échec
    if (total < chanceInitiale.value) { // Échec uniquement si le total est strictement inférieur
      if (chance.value > 0) {
        chance.value--
        // Sauvegarder dans localStorage
        const saved = localStorage.getItem(STORAGE_KEY)
        if (saved) {
          try {
            const data = JSON.parse(saved)
            data.chance = chance.value
            // Toujours garder la valeur initiale si elle existe, sinon l'initialiser
            if (!('chanceInitiale' in data)) {
              data.chanceInitiale = chanceInitiale.value
            }
            localStorage.setItem(STORAGE_KEY, JSON.stringify(data))
          } catch {}
        }
      }
    }
  }, 100)
}
</script>

<style scoped>
.chance-page {
  max-width: 400px;
  margin: 2rem auto 1.5rem auto;
  padding: 1.2rem 0.5rem 0.5rem 0.5rem;
  text-align: center;
}
.chance-card {
  background: #f8f8f8;
  border-radius: 18px;
  box-shadow: 0 2px 8px #b6f09c33;
  padding: 2em 1em 1.5em 1em;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.7em;
  margin-top: 2em;
}
.chance-icon {
  font-size: 2.5em;
  color: #b6f09c;
  margin-bottom: 0.2em;
}
.chance-label {
  font-size: 1.2em;
  color: #222;
  font-weight: 600;
}
.chance-value {
  font-size: 2.2em;
  font-weight: 700;
  color: #222;
  margin-top: 0.2em;
}
.chance-action {
  margin-top: 2.5em;
  color: #222;
  background: #f8f8f8;
  border-radius: 16px;
  box-shadow: 0 2px 8px #b6f09c22;
  padding: 1.2em 1em 1.5em 1em;
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.1em;
}
.bonus-malus-row {
  display: flex;
  align-items: center;
  gap: 0.7em;
  margin-bottom: 0.2em;
  justify-content: center;
}
.bonus-malus-input {
  width: 70px;
  font-size: 1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.3em 0.7em;
  background: #fff;
  color: #222;
}
.de-row {
  display: flex;
  justify-content: center;
  margin: 0.7em 0 0.5em 0;
}
.chance-btn {
  background: #b6f09c;
  color: #18191c;
  border: none;
  border-radius: 8px;
  padding: 0.7em 2em;
  font-size: 1.1em;
  font-weight: 700;
  margin: 1.2em auto 0 auto;
  display: block;
  box-shadow: 0 2px 8px #b6f09c22;
  transition: background 0.2s;
  cursor: pointer;
}
.chance-btn:disabled {
  background: #ccc;
  color: #888;
  cursor: not-allowed;
}
.chance-result {
  margin-top: 1em;
  font-size: 1.1em;
  color: #222;
}
.chance-zero {
  margin-top: 1em;
  color: #e44e6e;
  font-weight: 700;
  font-size: 1.1em;
}
</style> 