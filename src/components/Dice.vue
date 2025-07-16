<template>
  <div class="dice" :class="{ rolling }" :style="{ cursor: clickable ? 'pointer' : 'default' }" @click="clickable && roll()">
    <span v-if="!rolling">{{ value }}</span>
    <span v-else>{{ tempValue }}</span>
  </div>
</template>

<script setup>
import { ref, watch } from 'vue'
const props = defineProps({
  faces: { type: Number, default: 6 },
  modelValue: { type: Number, default: 1 },
  rolling: { type: Boolean, default: false },
  clickable: { type: Boolean, default: false },
})
const emit = defineEmits(['update:modelValue', 'roll-start', 'roll-end'])
const value = ref(props.modelValue)
const rolling = ref(false)
const tempValue = ref(value.value)

watch(() => props.rolling, (nv) => {
  if (nv) animateRoll()
})

function animateRoll() {
  rolling.value = true
  emit('roll-start')
  let count = 0
  const interval = setInterval(() => {
    tempValue.value = Math.floor(Math.random() * props.faces) + 1
    count++
    if (count > 15) {
      clearInterval(interval)
      value.value = Math.floor(Math.random() * props.faces) + 1
      emit('update:modelValue', value.value)
      rolling.value = false
      emit('roll-end', value.value)
    }
  }, 50)
}

watch(() => props.modelValue, (nv) => {
  value.value = nv
})
</script>

<style scoped>
.dice {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  width: 60px;
  height: 60px;
  border: 2px solid #333;
  border-radius: 10px;
  font-size: 2rem;
  background: #fff;
  margin: 0.5rem;
  transition: transform 0.2s;
  user-select: none;
  color: #111;
}
.dice span {
  color: #111;
  font-weight: 700;
  font-size: 2rem;
  line-height: 1;
}
.dice.rolling {
  animation: shake 0.7s linear;
}
@keyframes shake {
  0% { transform: rotate(0deg); }
  20% { transform: rotate(10deg); }
  40% { transform: rotate(-10deg); }
  60% { transform: rotate(10deg); }
  80% { transform: rotate(-10deg); }
  100% { transform: rotate(0deg); }
}
</style> 