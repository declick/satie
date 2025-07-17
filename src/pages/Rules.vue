// input combat
// input chance


<script setup>
import { ref } from 'vue'
const fight = ref('')
const chance = ref('')
</script>

<template>
  <div class="rules_page">
    <div class="input-row">
      <label class="input-label" for="fight">Combat</label>
      <input id="fight" v-model="fight" type="number" placeholder="Combat" />
    </div>
    <div class="input-row">
      <label class="input-label" for="chance">Chance</label>
      <input id="chance" v-model="chance" type="number" placeholder="Chance" />
      
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
.stats-cards {
  display: flex;
  flex-direction: row;
  gap: 1.2em;
  justify-content: center;
  margin-top: 2em;
}
.rules-card {
  background: #f8f8f8;
  border-radius: 14px;
  box-shadow: 0 2px 8px #b6f09c33;
  padding: 1.2em 1em 1em 1em;
  min-width: 90px;
  min-height: 80px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: 1.1em;
}
.rule-icon {
  font-size: 2em;
  margin-bottom: 0.2em;
  filter: drop-shadow(0 1px 2px #b6f09c33);
}
.rule-label {
  font-size: 1em;
  color: #42b883;
  font-weight: 600;
  margin-bottom: 0.1em;
}
.rule-value {
  font-size: 1.3em;
  font-weight: 700;
  color: #222;
}
</style>