<template>
  <div
    ref="eyeRef"
    class="eyeball"
    :class="{ 'eyeball--sad': isSad }"
    :style="{
      width: `${size}px`,
      height: isBlinking ? '2px' : isSad ? `${size * 0.5}px` : `${size}px`,
      backgroundColor: eyeColor,
      borderRadius: isSad ? `0 0 ${size}px ${size}px` : '50%',
      transform: isSad ? `rotate(${sadRotate}deg)` : 'rotate(0deg)',
    }"
  >
    <div
      v-if="!isBlinking"
      class="pupil"
      :style="{
        width: `${pupilSize}px`,
        height: `${pupilSize}px`,
        backgroundColor: pupilColor,
        transform: `translate(${pupilPosition.x}px, ${isSad ? -1 : pupilPosition.y}px)`,
      }"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted, onUnmounted } from 'vue'

const props = defineProps({
  size: {
    type: Number,
    default: 48
  },
  pupilSize: {
    type: Number,
    default: 16
  },
  maxDistance: {
    type: Number,
    default: 10
  },
  eyeColor: {
    type: String,
    default: 'white'
  },
  pupilColor: {
    type: String,
    default: 'black'
  },
  isBlinking: {
    type: Boolean,
    default: false
  },
  forceLookX: {
    type: Number,
    default: undefined
  },
  forceLookY: {
    type: Number,
    default: undefined
  },
  isSad: {
    type: Boolean,
    default: false
  },
  sadRotate: {
    type: Number,
    default: 0
  }
})

const eyeRef = ref(null)
const mouseX = ref(0)
const mouseY = ref(0)

const handleMouseMove = (e) => {
  mouseX.value = e.clientX
  mouseY.value = e.clientY
}

const pupilPosition = computed(() => {
  if (!eyeRef.value) return { x: 0, y: 0 }

  // If forced look direction is provided, use that instead of mouse tracking
  if (props.forceLookX !== undefined && props.forceLookY !== undefined) {
    return { x: props.forceLookX, y: props.forceLookY }
  }

  const eye = eyeRef.value.getBoundingClientRect()
  const eyeCenterX = eye.left + eye.width / 2
  const eyeCenterY = eye.top + eye.height / 2

  const deltaX = mouseX.value - eyeCenterX
  const deltaY = mouseY.value - eyeCenterY
  const distance = Math.min(Math.sqrt(deltaX ** 2 + deltaY ** 2), props.maxDistance)

  const angle = Math.atan2(deltaY, deltaX)
  const x = Math.cos(angle) * distance
  const y = Math.sin(angle) * distance

  return { x, y }
})

onMounted(() => {
  window.addEventListener('mousemove', handleMouseMove)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove)
})
</script>

<style scoped>
.eyeball {
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  overflow: hidden;
  will-change: height, border-radius, transform;
}

.pupil {
  border-radius: 50%;
  transition: transform 0.1s ease-out;
  will-change: transform;
}
</style>
