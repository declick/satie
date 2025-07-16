<template>
  <nav class="pillnav">
    <ul>
      <li :class="{ active: current === 'home' }" @click="$emit('navigate', 'home')">
        <span class="pill" v-if="current === 'home'">
          <span class="icon home">🏠</span>
          <span class="label">Home</span>
        </span>
        <span v-else class="icon home">🏠</span>
      </li>
      <li :class="{ active: current === 'perso' }" @click="$emit('navigate', 'perso')">
        <span class="pill" v-if="current === 'perso'">
          <span class="icon perso">🎲</span>
          <span class="label">Perso</span>
        </span>
        <span v-else class="icon perso">🎲</span>
      </li>
      <li :class="{ active: current === 'ennemis' }" @click="$emit('navigate', 'ennemis')">
        <span class="pill" v-if="current === 'ennemis'">
          <span class="icon ennemis">👹</span>
          <span class="label">Ennemis</span>
        </span>
        <span v-else class="icon ennemis">👹</span>
      </li>
      <li :class="{ active: current === 'chance' }" @click="$emit('navigate', 'chance')">
        <span class="pill" v-if="current === 'chance'">
          <span class="icon chance">🍀</span>
          <span class="label">Chance</span>
        </span>
        <span v-else class="icon chance">🍀</span>
      </li>
    </ul>
  </nav>
</template>

<script setup>
const props = defineProps({
  current: { type: String, default: 'home' }
})
</script>

<style scoped>
.pillnav {
  width: 95vw;
  max-width: 500px;
  left: 50%;
  transform: translateX(-50%);
  background: linear-gradient(135deg, #036e76, #2a2a2a);
  border-radius: 32px;
  color: #b6f09c;
  height: 64px;
  position: fixed;
  bottom: 18px;
  display: flex;
  align-items: center;
  z-index: 100;
  transition: box-shadow 0.2s, background 0.2s;
  overflow-x: hidden;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.3);
  padding: 0 1.2em;
}
ul {
  width: 100%;
  display: flex;
  justify-content: space-evenly;
  align-items: center;
  list-style: none;
  padding: 0;
  margin: 0;
  gap: 1em;
}
li {
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 1em;
  border-radius: 22px;
  position: relative;
  transition: background 0.18s, color 0.18s, transform 0.18s;
  min-width: 56px;
  min-height: 44px;
  cursor: pointer;
}
.icon {
  font-size: 1.7em;
  color: #b6f09c;
  transition: color 0.2s;
  display: flex;
  align-items: center;
  justify-content: center;
}
li.active .icon {
  color: #18191c;
}
.pill {
  display: flex;
  align-items: center;
  gap: 0.7em;
  background: #ffffff;
  color: #18191c;
  border-radius: 22px;
  padding: 0.1em 1em;
  font-weight: 600;
  font-size: 1.08em;
  min-width: 0;
  min-height: 0;
  border: 1.5px solid #eafbe0;
  animation: pillIn 0.22s cubic-bezier(.4,1.4,.6,1) both;
}
@keyframes pillIn {
  0% { transform: scale(0.8); opacity: 0.2; }
  100% { transform: scale(1); opacity: 1; }
}
.pill .icon {
  color: #18191c;
  font-size: 1.7em;
}
.label {
  font-size: 1.08em;
  color: #18191c;
  font-weight: 600;
  margin-left: 0.1em;
  letter-spacing: 0.01em;
  opacity: 1;
  display: inline;
  font-family: 'Inter', 'Segoe UI', Arial, sans-serif;
}
li:not(.active) .label {
  display: none;
}
@media (max-width: 500px) {
  .pillnav {
    height: 54px;
    border-radius: 18px;
    max-width: 99vw;
    padding: 0 0.3em;
  }
  ul {
    gap: 0.5em;
  }
  .icon {
    font-size: 1.2em;
  }
  .pill {
    font-size: 0.98em;
    padding: 0.3em 0.6em;
  }
  .label {
    font-size: 0.85em;
  }
}
</style> 