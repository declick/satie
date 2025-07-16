<template>
  <nav class="bottomnav">
    <ul>
      <li :class="{ active: current === 'home' }" @click="$emit('navigate', 'home')">
        <span class="icon home">🏠</span>
        <span class="label">Home</span>
      </li>
      <li :class="{ active: current === 'perso' }" @click="$emit('navigate', 'perso')">
        <span class="icon perso">🎲</span>
        <span class="label">Perso</span>
      </li>
      <li :class="{ active: current === 'ennemis' }" @click="$emit('navigate', 'ennemis')">
        <span class="icon ennemis">👹</span>
        <span class="label">Ennemis</span>
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
.bottomnav {
  width: 96vw;
  max-width: 500px;
  left: 50%;
  transform: translateX(-50%);
  background: rgba(34,34,34,0.85);
  backdrop-filter: blur(8px) saturate(1.2);
  border-radius: 22px 22px 18px 18px;
  box-shadow: 0 8px 32px #0004, 0 1.5px 8px #42b88344;
  color: #fff;
  height: 70px;
  position: fixed;
  bottom: 18px;
  display: flex;
  align-items: center;
  z-index: 100;
  border: 1.5px solid #42b88333;
  transition: box-shadow 0.2s;
}
ul {
  width: 100%;
  display: flex;
  justify-content: space-around;
  align-items: center;
  list-style: none;
  padding: 0;
  margin: 0;
}
li {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 0.1em;
  padding: 0.2em 0.7em 0 0.7em;
  cursor: pointer;
  border-top: 3px solid transparent;
  transition: background 0.2s, border-color 0.2s, transform 0.18s;
  font-size: 1em;
  border-radius: 12px 12px 0 0;
  position: relative;
}
li.active {
  background: rgba(66,184,131,0.13);
  border-top: 3px solid #42b883;
  transform: translateY(-7px) scale(1.12);
  box-shadow: 0 4px 16px #42b88333;
}
li:active {
  transform: scale(0.97);
}
.icon {
  font-size: 2.1em;
  filter: drop-shadow(0 2px 4px #0002);
  transition: color 0.2s, filter 0.2s;
}
.icon.home {
  color: #6ec1e4;
}
.icon.perso {
  color: #42b883;
}
.icon.ennemis {
  color: #e44e6e;
}
li.active .icon {
  filter: drop-shadow(0 0 8px #42b88388);
}
.label {
  font-size: 0.85em;
  color: #eee;
  margin-top: 0.1em;
  letter-spacing: 0.01em;
  font-weight: 500;
  opacity: 0.85;
}
@media (max-width: 400px) {
  .bottomnav {
    height: 62px;
    border-radius: 18px 18px 14px 14px;
  }
  .icon {
    font-size: 1.5em;
  }
  .label {
    font-size: 0.7em;
  }
}
</style> 