<template>
  <div class="combat-page">
    <h1>Fiche Ennemi</h1>
    <form v-if="!valide && !combatEnCours" @submit.prevent>
      <div class="carac-row">
        <label>Habilité</label>
        <input type="number" v-model="habilete" min="0" inputmode="numeric" />
      </div>
      <div class="carac-row">
        <label>Endurance</label>
        <input type="number" v-model="endurance" min="0" inputmode="numeric" />
      </div>
      <button class="valider-btn" @click="valide = true">Valider</button>
    </form>
    <div v-else>
      <div v-if="!combatEnCours">
        <h2>Stats de l'ennemi</h2>
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
        </div>
        <button class="valider-btn" @click="demarrerCombat">Commencer le combat</button>
        <button class="modifier-btn" @click="valide = false">Modifier</button>
      </div>
      <div v-else class="combat-box">
        <h2>Combat en cours</h2>
        <div class="combat-stats">
          <div class="combat-col">
            <div class="combat-label">Héros</div>
            <div class="combat-value">Habilité : {{ heroHabilete }}</div>
            <div class="combat-value">Endurance : {{ heroEndurance }}</div>
          </div>
          <div class="combat-col">
            <div class="combat-label">Ennemi</div>
            <div class="combat-value">Habilité : {{ ennemiHabilete }}</div>
            <div class="combat-value">Endurance : {{ ennemiEndurance }}</div>
          </div>
        </div>
        <div v-if="!combatFini">
          <div class="dice-block">
            <div class="dice-title">Tour {{ tour }}</div>
            <div v-if="puissanceEnnemi === null">
              <div class="bonus-malus-row">
                <label>Bonus/Malus ennemi :</label>
                <input type="number" v-model.number="bonusMalusEnnemi" inputmode="numeric" class="bonus-malus-input" placeholder="+2 ou -1" />
              </div>
              <div class="dice-label">Lancer pour l'ennemi :</div>
              <div class="des-row">
                <Dice :faces="6" v-model="de1Ennemi" :rolling="rollingEnnemi" :clickable="false" @roll-end="onRollEndEnnemi" />
                <Dice :faces="6" v-model="de2Ennemi" :rolling="rollingEnnemi" :clickable="false" />
              </div>
              <button class="valider-btn" @click="lancerDesEnnemi" :disabled="rollingEnnemi || rollingHero">Lancer pour l'ennemi</button>
            </div>
            <div v-else>
              <div v-if="puissanceEnnemi !== null" class="dice-result">
                Puissance d'attaque ennemi :
                <b>{{ puissanceEnnemi }}</b>
                <span v-if="bonusMalusEnnemi || bonusMalusEnnemi === 0"> ({{ scoreBrutEnnemi }} {{ bonusMalusEnnemi >= 0 ? '+' : '' }}{{ bonusMalusEnnemi }})</span>
              </div>
              <div v-if="puissanceHero === null">
                <div class="bonus-malus-row">
                  <label>Bonus/Malus héros :</label>
                  <input type="number" v-model.number="bonusMalusHero" inputmode="numeric" class="bonus-malus-input" placeholder="+2 ou -1" />
                </div>
                <div class="dice-label">Lancer pour le héros :</div>
                <div class="des-row">
                  <Dice :faces="6" v-model="de1Hero" :rolling="rollingHero" :clickable="false" @roll-end="onRollEndHero" />
                  <Dice :faces="6" v-model="de2Hero" :rolling="rollingHero" :clickable="false" />
                </div>
                <button class="valider-btn" @click="lancerDesHero" :disabled="rollingHero">Lancer pour le héros</button>
              </div>
              <div v-if="puissanceHero !== null">
                <div v-if="puissanceHero !== null" class="dice-result">
                  Puissance d'attaque héros :
                  <b>{{ puissanceHero }}</b>
                  <span v-if="bonusMalusHero || bonusMalusHero === 0"> ({{ scoreBrutHero }} {{ bonusMalusHero >= 0 ? '+' : '' }}{{ bonusMalusHero }})</span>
                </div>
                <div v-if="roundResult" class="round-result">{{ roundResult }}</div>
                <button class="valider-btn" @click="tourSuivant">Tour suivant</button>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="fin-combat">
          <h3>Fin du combat</h3>
          <div v-if="ennemiEndurance <= 0" class="victoire">Victoire du héros !</div>
          <div v-else class="defaite">Défaite du héros...</div>
          <button class="modifier-btn" @click="resetCombat">Nouveau combat</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from 'vue'
import Dice from '../components/Dice.vue'
const emit = defineEmits(['player-dead'])
const STORAGE_KEY = 'ennemi-v1'
const HERO_KEY = 'feuille-perso-v1'
const habilete = ref(0)
const endurance = ref(0)
const valide = ref(false)
const combatEnCours = ref(false)
const tour = ref(1)
const ennemiHabilete = ref(0)
const ennemiEndurance = ref(0)
const heroHabilete = ref(0)
const heroEndurance = ref(0)
const de1Ennemi = ref(1)
const de2Ennemi = ref(1)
const de1Hero = ref(1)
const de2Hero = ref(1)
const rollingEnnemi = ref(false)
const rollingHero = ref(false)
const puissanceEnnemi = ref(null)
const puissanceHero = ref(null)
const roundResult = ref('')
const combatFini = ref(false)
const bonusMalusHero = ref(0)
const bonusMalusEnnemi = ref(0)
const scoreBrutEnnemi = ref(0)
const scoreBrutHero = ref(0)

// Charger depuis localStorage au démarrage
onMounted(() => {
  const saved = localStorage.getItem(STORAGE_KEY)
  if (saved) {
    try {
      const data = JSON.parse(saved)
      habilete.value = data.habilete ?? 0
      endurance.value = data.endurance ?? 0
      valide.value = data.valide ?? false
    } catch {}
  }
  // Charger stats héros
  const hero = localStorage.getItem(HERO_KEY)
  if (hero) {
    try {
      const data = JSON.parse(hero)
      heroHabilete.value = data.habilete ?? 0
      heroEndurance.value = data.endurance ?? 0
    } catch {}
  }
})

// Sauvegarder à chaque modification
watch([
  habilete, endurance, valide
], () => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify({
    habilete: habilete.value,
    endurance: endurance.value,
    valide: valide.value
  }))
}, { deep: true })

// A modifier pour avoir toujours la stats du referentiel
function demarrerCombat() {
  ennemiHabilete.value = habilete.value
  ennemiEndurance.value = endurance.value
  // Recharger stats héros (au cas où modifiées)
  const hero = localStorage.getItem(HERO_KEY)
  if (hero) {
    try {
      const data = JSON.parse(hero)
      heroHabilete.value = data.habilete ?? 0
      heroEndurance.value = data.endurance ?? 0
    } catch {}
  }
  tour.value = 1
  puissanceEnnemi.value = null
  puissanceHero.value = null
  roundResult.value = ''
  combatFini.value = false
  combatEnCours.value = true
}
function lancerDesEnnemi() {
  puissanceEnnemi.value = null
  puissanceHero.value = null
  roundResult.value = ''
  de1Ennemi.value = Math.floor(Math.random() * 6) + 1
  de2Ennemi.value = Math.floor(Math.random() * 6) + 1
  rollingEnnemi.value = true
}
function onRollEndEnnemi() {
  setTimeout(() => {
    scoreBrutEnnemi.value = de1Ennemi.value + de2Ennemi.value + ennemiHabilete.value
    puissanceEnnemi.value = scoreBrutEnnemi.value + (Number(bonusMalusEnnemi.value) || 0)
    rollingEnnemi.value = false
  }, 100)
}
function lancerDesHero() {
  puissanceHero.value = null
  de1Hero.value = Math.floor(Math.random() * 6) + 1
  de2Hero.value = Math.floor(Math.random() * 6) + 1
  rollingHero.value = true
}
function onRollEndHero() {
  setTimeout(() => {
    scoreBrutHero.value = de1Hero.value + de2Hero.value + heroHabilete.value
    puissanceHero.value = scoreBrutHero.value + (Number(bonusMalusHero.value) || 0)
    rollingHero.value = false
    // Résultat du round
    if (puissanceHero.value > puissanceEnnemi.value) {
      ennemiEndurance.value -= 2
      roundResult.value = 'Le héros blesse l\'ennemi (-2 Endurance ennemi)';
    } else if (puissanceHero.value < puissanceEnnemi.value) {
      heroEndurance.value -= 2
      roundResult.value = 'Le héros est blessé (-2 Endurance héros)';
    } else {
      roundResult.value = 'Égalité, personne ne perd de vie.';
    }
    // Fin combat ?
    if (ennemiEndurance.value <= 0 || heroEndurance.value <= 0) {
      combatFini.value = true
      // Sauvegarder la nouvelle endurance du héros
      const hero = localStorage.getItem(HERO_KEY)
      if (hero) {
        try {
          const data = JSON.parse(hero)
          data.endurance = heroEndurance.value
          localStorage.setItem(HERO_KEY, JSON.stringify(data))
        } catch {}
      }
      // Si le héros est mort, prévenir le parent
      if (heroEndurance.value <= 0) {
        emit('player-dead')
      }
    }
  }, 100)
}
function tourSuivant() {
  tour.value++
  puissanceEnnemi.value = null
  puissanceHero.value = null
  roundResult.value = ''
}
function resetCombat() {
  combatEnCours.value = false
  tour.value = 1
  puissanceEnnemi.value = null
  puissanceHero.value = null
  roundResult.value = ''
  combatFini.value = false
  habilete.value = 0
  endurance.value = 0
  valide.value = false
}
</script>

<style scoped>
.combat-page {
  max-width: 400px;
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
.stats-cards {
  display: flex;
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
.dice-block {
  background: #f8f8f8;
  border-radius: 12px;
  padding: 1em 1em 1.2em 1em;
  margin-bottom: 1.2em;
  text-align: center;
  box-shadow: 0 2px 8px #42b88322;
}
.des-row {
  display: flex;
  justify-content: center;
  gap: 1em;
  margin: 1em 0 0.5em 0;
}
.dice-result {
  margin-top: 0.7em;
  font-size: 1.2em;
  color: #42b883;
  font-weight: 600;
}
select {
  font-size: 1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.2em 0.7em;
  margin-left: 0.7em;
  margin-bottom: 0.5em;
}
.combat-box {
  background: #f8f8f8;
  color: #222; 
  border-radius: 12px;
  padding: 1.2em 1em 1.2em 1em;
  margin-bottom: 1.2em;
  box-shadow: 0 2px 8px #42b88322;
}
.combat-stats {
  display: flex;
  justify-content: space-between;
  gap: 1.5em;
  margin-bottom: 1.2em;
}
.combat-col {
  background: #e6f7f1;
  border-radius: 10px;
  padding: 0.7em 1em;
  min-width: 120px;
  text-align: center;
}
.combat-label {
  font-weight: 700;
  color: #42b883;
  margin-bottom: 0.2em;
}
.combat-value {
  font-size: 1.1em;
  color: #222;
  margin-bottom: 0.2em;
}
.dice-title {
  font-weight: 700;
  color: #222;
  margin-bottom: 0.5em;
}
.dice-label {
  font-weight: 600;
  color: #888;
  margin-top: 0.7em;
  margin-bottom: 0.2em;
}
.round-result {
  margin: 1em 0 0.5em 0;
  font-size: 1.1em;
  font-weight: 600;
  color: #e44e6e;
}
.victoire {
  color: #42b883;
  font-weight: 700;
  font-size: 1.2em;
  margin: 1em 0;
}
.defaite {
  color: #e44e6e;
  font-weight: 700;
  font-size: 1.2em;
  margin: 1em 0;
}
.bonus-malus-row {
  display: flex;
  align-items: center;
  gap: 0.7em;
  margin-bottom: 1em;
  margin-top: 0.5em;
}
.bonus-malus-input {
  width: 60px;
  font-size: 1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.3em 0.7em;
  background: #fff;
  color: #222;
}
.bonus-malus-hint {
  color: #888;
  font-size: 0.95em;
}
@media (max-width: 500px) {
  .combat-page {
    padding: 0.5rem 0.1rem 0.5rem 0.1rem;
  }
  .carac-row input {
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
}
</style> 