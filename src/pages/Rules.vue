<script setup>
import { ref, onMounted, watch } from 'vue'
const fightLoss = ref(-2) // PV perdus en combat (défaut -2)
const chanceLoss = ref(-1) // Points de chance perdus si échec (défaut -1)

onMounted(() => {
  const saved = localStorage.getItem('rules')
  if (saved) {
    const data = JSON.parse(saved)
    fightLoss.value = data.fightLoss ?? -2
    chanceLoss.value = data.chanceLoss ?? -1
  }
})

watch([fightLoss, chanceLoss], () => {
  localStorage.setItem('rules', JSON.stringify({
    fightLoss: fightLoss.value,
    chanceLoss: chanceLoss.value
  }))
})
</script>

<template>
  <div class="rules_page">
    <div class="input-row">
      <label class="input-label" for="fightLoss">PV perdus en combat</label>
      <input id="fightLoss" v-model="fightLoss" type="number" placeholder="-2" />
    </div>
    <div class="input-row">
      <label class="input-label" for="chanceLoss">Points de chance perdus si échec</label>
      <input id="chanceLoss" v-model="chanceLoss" type="number" placeholder="-1" />
    </div>
  </div>
</template>

<style scoped>
.rules_page {
  max-width: 400px;
  margin: 2rem auto 1.5rem auto;
  padding: 1.2rem 0.5rem 0.5rem 0.5rem;
}
.input-row {
  display: flex;
  align-items: center;
  justify-content: space-between;
  gap: 1em;
  margin-bottom: 1em;
  width: 100%;
}
.input-label {
  min-width: 70px;
  text-align: left;
  font-weight: 600;
  color: #ffffff;
  font-size: 1.1em;
}
input[type="number"] {
  flex: 1;
  max-width: 180px;
  font-size: 1.1em;
  border-radius: 6px;
  border: 1px solid #bbb;
  padding: 0.3em 0.7em;
  background: #fff;
  color: #222;
  margin-bottom: 0;
  margin-left: 1em;
}
input[type="number"]:focus {
  outline: 2px solid #b6f09c;
  border-color: #42b883;
}
</style>