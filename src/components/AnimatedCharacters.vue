<template>
  <div class="animated-characters-container">
    <!-- Confetti -->
    <div v-if="showConfetti" class="confetti-container">
      <div v-for="(style, i) in confettiStyles" :key="i" class="confetti-piece" :style="style" />
    </div>
    <!-- Purple tall rectangle character - Back layer -->
    <div
      ref="purpleRef"
      class="character purple-character"
      :class="{ 'entrance-complete': hasEntered }"
      :style="{
        left: '70px',
        width: '180px',
        height: (isTyping || isHidingPassword) ? '440px' : '400px',
        backgroundColor: '#6C3FF5',
        borderRadius: '0',
        zIndex: 1,
        transform: hasEntered
          ? ((passwordLength > 0 && showPassword)
            ? 'skewX(0deg)'
            : (isTyping || isHidingPassword)
              ? `skewX(${(purplePos.bodySkew || 0) - 12}deg) translateX(40px)`
              : `skewX(${purplePos.bodySkew || 0}deg)`)
          : undefined,
      }"
    >
      <!-- Eyes -->
      <div
        class="eyes"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '50px' : isLookingAtEachOther ? '85px' : `${75 + purplePos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '20px' : isLookingAtEachOther ? '50px' : `${25 + purplePos.faceY}px`,
        }"
      >
        <EyeBall
          :size="18"
          :pupilSize="7"
          :maxDistance="5"
          eyeColor="white"
          pupilColor="#2D2D2D"
          :isBlinking="isPurpleBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? (isPurplePeeking ? 4 : -4) : isLookingAtEachOther ? 3 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? (isPurplePeeking ? 5 : -4) : isLookingAtEachOther ? 4 : undefined"
        />
        <EyeBall
          :size="18"
          :pupilSize="7"
          :maxDistance="5"
          eyeColor="white"
          pupilColor="#2D2D2D"
          :isBlinking="isPurpleBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? (isPurplePeeking ? 4 : -4) : isLookingAtEachOther ? 3 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? (isPurplePeeking ? 5 : -4) : isLookingAtEachOther ? 4 : undefined"
        />
      </div>
      <!-- Mouth -->
      <div
        class="purple-mouth-shape"
        :class="{
          'purple-mouth-shape--typing': (isTyping || isHidingPassword) && !loginFailed && !loginSuccess,
          'purple-mouth-shape--sad': loginFailed,
          'purple-mouth-shape--happy': loginSuccess,
        }"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '72px' : isLookingAtEachOther ? '106px' : `${97 + purplePos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '57px' : isLookingAtEachOther ? '82px' : `${57 + purplePos.faceY}px`,
          '--counter-skew': (isTyping || isHidingPassword)
            ? `skewX(${-((purplePos.bodySkew || 0) - 12)}deg)`
            : 'skewX(0deg)',
        }"
      />
    </div>

    <!-- Black tall rectangle character - Middle layer -->
    <div
      ref="blackRef"
      class="character black-character"
      :class="{ 'entrance-complete': hasEntered }"
      :style="{
        left: '240px',
        width: '120px',
        height: '310px',
        backgroundColor: '#2D2D2D',
        borderRadius: '0',
        zIndex: 2,
        transform: hasEntered
          ? ((passwordLength > 0 && showPassword)
            ? 'skewX(0deg)'
            : isLookingAtEachOther
              ? `skewX(${(blackPos.bodySkew || 0) * 1.5 + 10}deg) translateX(20px)`
              : (isTyping || isHidingPassword)
                ? `skewX(${(blackPos.bodySkew || 0) * 1.5}deg)`
                : `skewX(${blackPos.bodySkew || 0}deg)`)
          : undefined,
      }"
    >
      <!-- Eyes -->
      <div
        class="eyes"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '10px' : isLookingAtEachOther ? '32px' : `${26 + blackPos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '28px' : isLookingAtEachOther ? '12px' : `${32 + blackPos.faceY}px`,
        }"
      >
        <EyeBall
          :size="16"
          :pupilSize="6"
          :maxDistance="4"
          eyeColor="white"
          pupilColor="#2D2D2D"
          :isBlinking="isBlackBlinking"
          :isSad="loginFailed"
          :sadRotate="-20"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -4 : isLookingAtEachOther ? 0 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : isLookingAtEachOther ? -4 : undefined"
        />
        <EyeBall
          :size="16"
          :pupilSize="6"
          :maxDistance="4"
          eyeColor="white"
          pupilColor="#2D2D2D"
          :isBlinking="isBlackBlinking"
          :isSad="loginFailed"
          :sadRotate="20"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -4 : isLookingAtEachOther ? 0 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : isLookingAtEachOther ? -4 : undefined"
        />
      </div>
    </div>

    <!-- Orange semi-circle character - Front left -->
    <div
      ref="orangeRef"
      class="character orange-character"
      :class="{ 'entrance-complete': hasEntered }"
      :style="{
        left: '0px',
        width: '240px',
        height: '150px',
        zIndex: 3,
        backgroundColor: '#FF9B6B',
        borderRadius: '120px 120px 0 0',
        transform: hasEntered
          ? ((passwordLength > 0 && showPassword) ? 'skewX(0deg)' : `skewX(${orangePos.bodySkew || 0}deg)`)
          : undefined,
      }"
    >
      <!-- Eyes (just pupils) -->
      <div
        class="eyes"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '80px' : `${112 + orangePos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '55px' : `${60 + orangePos.faceY}px`,
        }"
      >
        <Pupil
          :size="12"
          :maxDistance="5"
          pupilColor="#2D2D2D"
          :isBlinking="isOrangeBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -5 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : undefined"
        />
        <Pupil
          :size="12"
          :maxDistance="5"
          pupilColor="#2D2D2D"
          :isBlinking="isOrangeBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -5 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : undefined"
        />
      </div>
      <!-- Mouth -->
      <div
        class="orange-mouth-shape"
        :class="{
          'orange-mouth-shape--typing': (isTyping || isHidingPassword) && !loginFailed && !loginSuccess,
          'orange-mouth-shape--sad': loginFailed,
          'orange-mouth-shape--happy': loginSuccess,
        }"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '94px' : `${126 + orangePos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '87px' : `${92 + orangePos.faceY}px`,
        }"
      />
    </div>

    <!-- Yellow tall rectangle character - Front right -->
    <div
      ref="yellowRef"
      class="character yellow-character"
      :class="{ 'entrance-complete': hasEntered }"
      :style="{
        left: '310px',
        width: '140px',
        height: '230px',
        backgroundColor: '#E8D754',
        borderRadius: '70px 70px 0 0',
        zIndex: 4,
        transform: hasEntered
          ? ((passwordLength > 0 && showPassword) ? 'skewX(0deg)' : `skewX(${yellowPos.bodySkew || 0}deg)`)
          : undefined,
      }"
    >
      <!-- Eyes (just pupils) -->
      <div
        class="eyes"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '20px' : `${52 + yellowPos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '35px' : `${40 + yellowPos.faceY}px`,
        }"
      >
        <Pupil
          :size="12"
          :maxDistance="5"
          pupilColor="#2D2D2D"
          :isBlinking="isYellowBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -5 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : undefined"
        />
        <Pupil
          :size="12"
          :maxDistance="5"
          pupilColor="#2D2D2D"
          :isBlinking="isYellowBlinking"
          :forceLookX="loginSuccess ? 0 : (passwordLength > 0 && showPassword) ? -5 : undefined"
          :forceLookY="loginSuccess ? successLookY : (passwordLength > 0 && showPassword) ? -4 : undefined"
        />
      </div>
      <!-- Mouth -->
      <div
        class="yellow-mouth-wrapper"
        :style="{
          left: (passwordLength > 0 && showPassword) ? '10px' : `${40 + yellowPos.faceX}px`,
          top: (passwordLength > 0 && showPassword) ? '88px' : `${88 + yellowPos.faceY}px`,
        }"
      >
        <svg width="80" height="20" viewBox="0 0 80 20">
          <path
            class="yellow-mouth-path"
            :class="{ 'yellow-mouth-path--wavy': loginFailed, 'yellow-mouth-path--happy': loginSuccess }"
            stroke="#2D2D2D"
            stroke-width="3"
            fill="none"
            stroke-linecap="round"
          />
        </svg>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, watch, onMounted, onUnmounted } from 'vue'
import EyeBall from './EyeBall.vue'
import Pupil from './Pupil.vue'

const props = defineProps({
  isTyping: {
    type: Boolean,
    default: false
  },
  showPassword: {
    type: Boolean,
    default: false
  },
  passwordLength: {
    type: Number,
    default: 0
  },
  loginFailed: {
    type: Boolean,
    default: false
  },
  loginSuccess: {
    type: Boolean,
    default: false
  }
})

const purpleRef = ref(null)
const blackRef = ref(null)
const orangeRef = ref(null)
const yellowRef = ref(null)

const hasEntered = ref(false)

const mouseX = ref(0)
const mouseY = ref(0)
const isPurpleBlinking = ref(false)
const isBlackBlinking = ref(false)
const isOrangeBlinking = ref(false)
const isYellowBlinking = ref(false)
const isLookingAtEachOther = ref(false)
const isPurplePeeking = ref(false)

const purplePos = ref({ faceX: 0, faceY: 0, bodySkew: 0 })
const blackPos = ref({ faceX: 0, faceY: 0, bodySkew: 0 })
const orangePos = ref({ faceX: 0, faceY: 0, bodySkew: 0 })
const yellowPos = ref({ faceX: 0, faceY: 0, bodySkew: 0 })

const isHidingPassword = computed(() => props.passwordLength > 0 && !props.showPassword)

const confettiColors = ['#FF6B6B', '#4ECDC4', '#FFE66D', '#A78BFA', '#FF9B6B', '#6BCB77', '#4D96FF']
const confettiStyles = ref([])
const showConfetti = ref(false)
let confettiTimeout = null

const generateConfetti = () => {
  confettiStyles.value = Array.from({ length: 180 }, (_, i) => {
    const color = confettiColors[i % confettiColors.length]
    return {
      left: `${Math.random() * 100}%`,
      top: `-${10 + Math.random() * 30}%`,
      backgroundColor: color,
      width: `${4 + Math.random() * 6}px`,
      height: `${8 + Math.random() * 12}px`,
      animationDelay: `${Math.random() * 2}s`,
      animationDuration: `${4.5 + Math.random() * 2}s`,
      transform: `rotate(${Math.random() * 360}deg)`,
    }
  })
  showConfetti.value = true

  // 清理confetti DOM元素，避免内存泄漏
  if (confettiTimeout) clearTimeout(confettiTimeout)
  confettiTimeout = setTimeout(() => {
    showConfetti.value = false
    confettiStyles.value = []
  }, 8000) // 动画最长6.5秒，留1.5秒缓冲
}

const successLookY = ref(-5)
let successLookAnimId = null

const animateSuccessLook = () => {
  const startY = -5
  const endY = 4
  const duration = 5500
  const startTime = performance.now()

  const step = (now) => {
    const elapsed = now - startTime
    const progress = Math.min(elapsed / duration, 1)
    // ease-in-out cubic
    const eased = progress < 0.5
      ? 4 * progress * progress * progress
      : 1 - Math.pow(-2 * progress + 2, 3) / 2
    successLookY.value = startY + (endY - startY) * eased
    if (progress < 1) {
      successLookAnimId = requestAnimationFrame(step)
    }
  }
  successLookAnimId = requestAnimationFrame(step)
}

watch(() => props.loginSuccess, (val) => {
  if (val) {
    generateConfetti()
    successLookY.value = -5
    if (successLookAnimId) cancelAnimationFrame(successLookAnimId)
    animateSuccessLook()
  } else {
    successLookY.value = -5
    if (successLookAnimId) cancelAnimationFrame(successLookAnimId)
  }
})

let purpleBlinkTimeout = null
let blackBlinkTimeout = null
let orangeBlinkTimeout = null
let yellowBlinkTimeout = null
let lookingTimeout = null
let peekTimeout = null

// 缓存角色中心位置，避免每次mousemove都调用getBoundingClientRect
const characterCenters = ref({
  purple: { x: 0, y: 0 },
  black: { x: 0, y: 0 },
  orange: { x: 0, y: 0 },
  yellow: { x: 0, y: 0 }
})

const updateCharacterCenters = () => {
  if (purpleRef.value) {
    const rect = purpleRef.value.getBoundingClientRect()
    characterCenters.value.purple = { x: rect.left + rect.width / 2, y: rect.top + rect.height / 3 }
  }
  if (blackRef.value) {
    const rect = blackRef.value.getBoundingClientRect()
    characterCenters.value.black = { x: rect.left + rect.width / 2, y: rect.top + rect.height / 3 }
  }
  if (orangeRef.value) {
    const rect = orangeRef.value.getBoundingClientRect()
    characterCenters.value.orange = { x: rect.left + rect.width / 2, y: rect.top + rect.height / 3 }
  }
  if (yellowRef.value) {
    const rect = yellowRef.value.getBoundingClientRect()
    characterCenters.value.yellow = { x: rect.left + rect.width / 2, y: rect.top + rect.height / 3 }
  }
}

const calculatePosition = (centerX, centerY, mx, my, rangeX = 15, rangeY = 10, minX = null, maxX = null, minY = null, maxY = null) => {
  const rMinX = minX !== null ? minX : -rangeX
  const rMaxX = maxX !== null ? maxX : rangeX
  const rMinY = minY !== null ? minY : -rangeY
  const rMaxY = maxY !== null ? maxY : rangeY

  const deltaX = mx - centerX
  const deltaY = my - centerY

  const scaleX = Math.max(Math.abs(rMinX), Math.abs(rMaxX))
  const scaleY = Math.max(Math.abs(rMinY), Math.abs(rMaxY))
  const faceX = Math.max(rMinX, Math.min(rMaxX, deltaX / (300 / scaleX)))
  const faceY = Math.max(rMinY, Math.min(rMaxY, deltaY / (300 / scaleY)))
  const bodySkew = Math.max(-6, Math.min(6, -deltaX / 120))

  return { faceX, faceY, bodySkew }
}

let rafId = null
let pendingMouseX = 0
let pendingMouseY = 0
let needsUpdate = false

const updatePositions = () => {
  if (needsUpdate && hasEntered.value) {
    needsUpdate = false
    mouseX.value = pendingMouseX
    mouseY.value = pendingMouseY

    const { purple, black, orange, yellow } = characterCenters.value
    purplePos.value = calculatePosition(purple.x, purple.y, pendingMouseX, pendingMouseY, 0, 0, -46, 18, -8, 5)
    blackPos.value = calculatePosition(black.x, black.y, pendingMouseX, pendingMouseY)
    orangePos.value = calculatePosition(orange.x, orange.y, pendingMouseX, pendingMouseY, 0, 0, -46, 20, -18, 20)
    yellowPos.value = calculatePosition(yellow.x, yellow.y, pendingMouseX, pendingMouseY)
  }
  rafId = requestAnimationFrame(updatePositions)
}

const handleMouseMove = (e) => {
  pendingMouseX = e.clientX
  pendingMouseY = e.clientY
  needsUpdate = true
}

// Purple blinking effect
const schedulePurpleBlink = () => {
  const interval = Math.random() * 4000 + 3000
  purpleBlinkTimeout = setTimeout(() => {
    isPurpleBlinking.value = true
    setTimeout(() => {
      isPurpleBlinking.value = false
      schedulePurpleBlink()
    }, 150)
  }, interval)
}

// Black blinking effect
const scheduleBlackBlink = () => {
  const interval = Math.random() * 4000 + 3000
  blackBlinkTimeout = setTimeout(() => {
    isBlackBlinking.value = true
    setTimeout(() => {
      isBlackBlinking.value = false
      scheduleBlackBlink()
    }, 150)
  }, interval)
}

// Orange blinking effect
const scheduleOrangeBlink = () => {
  const interval = Math.random() * 4000 + 3000
  orangeBlinkTimeout = setTimeout(() => {
    isOrangeBlinking.value = true
    setTimeout(() => {
      isOrangeBlinking.value = false
      scheduleOrangeBlink()
    }, 150)
  }, interval)
}

// Yellow blinking effect
const scheduleYellowBlink = () => {
  const interval = Math.random() * 4000 + 3000
  yellowBlinkTimeout = setTimeout(() => {
    isYellowBlinking.value = true
    setTimeout(() => {
      isYellowBlinking.value = false
      scheduleYellowBlink()
    }, 150)
  }, interval)
}

// Looking at each other when typing
watch(() => props.isTyping, (newVal) => {
  if (newVal) {
    isLookingAtEachOther.value = true
    lookingTimeout = setTimeout(() => {
      isLookingAtEachOther.value = false
    }, 800)
  } else {
    isLookingAtEachOther.value = false
    if (lookingTimeout) clearTimeout(lookingTimeout)
  }
})

// Purple peeking when password is visible
watch([() => props.passwordLength, () => props.showPassword, isPurplePeeking], ([length, show, peeking]) => {
  if (length > 0 && show && !peeking) {
    const interval = Math.random() * 3000 + 2000
    peekTimeout = setTimeout(() => {
      isPurplePeeking.value = true
      setTimeout(() => {
        isPurplePeeking.value = false
      }, 800)
    }, interval)
  } else if (length === 0 || !show) {
    isPurplePeeking.value = false
    if (peekTimeout) clearTimeout(peekTimeout)
  }
})

onMounted(() => {
  window.addEventListener('mousemove', handleMouseMove, { passive: true })
  window.addEventListener('resize', updateCharacterCenters, { passive: true })
  schedulePurpleBlink()
  scheduleBlackBlink()
  scheduleOrangeBlink()
  scheduleYellowBlink()

  // Trigger entrance animation completion after all animations finish
  // Purple: 1.2s, Black: 1s + 0.2s delay = 1.2s, Orange: 1.1s + 0.1s = 1.2s, Yellow: 1s + 0.3s = 1.3s
  // Wait for the longest animation (Yellow at 1.3s) plus a small buffer
  setTimeout(() => {
    hasEntered.value = true
    updateCharacterCenters()
    rafId = requestAnimationFrame(updatePositions)
  }, 1400)
})

onUnmounted(() => {
  window.removeEventListener('mousemove', handleMouseMove)
  window.removeEventListener('resize', updateCharacterCenters)
  if (purpleBlinkTimeout) clearTimeout(purpleBlinkTimeout)
  if (blackBlinkTimeout) clearTimeout(blackBlinkTimeout)
  if (orangeBlinkTimeout) clearTimeout(orangeBlinkTimeout)
  if (yellowBlinkTimeout) clearTimeout(yellowBlinkTimeout)
  if (lookingTimeout) clearTimeout(lookingTimeout)
  if (peekTimeout) clearTimeout(peekTimeout)
  if (successLookAnimId) cancelAnimationFrame(successLookAnimId)
  if (rafId) cancelAnimationFrame(rafId)
  if (confettiTimeout) clearTimeout(confettiTimeout)
})
</script>

<style scoped>
.animated-characters-container {
  position: relative;
  width: 550px;
  height: 400px;
}

.character {
  position: absolute;
  bottom: 0;
  transition: all 0.7s cubic-bezier(0.4, 0, 0.2, 1);
  transform-origin: bottom center;
  will-change: transform;
}

/* Entrance animations */
.purple-character {
  animation: purple-entrance 1.2s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

.purple-character.entrance-complete {
  animation: none;
}

.black-character {
  animation: black-entrance 1s cubic-bezier(0.34, 1.56, 0.64, 1) 0.2s backwards;
}

.black-character.entrance-complete {
  animation: none;
}

.orange-character {
  animation: orange-entrance 1.1s cubic-bezier(0.34, 1.56, 0.64, 1) 0.1s backwards;
}

.orange-character.entrance-complete {
  animation: none;
}

.yellow-character {
  animation: yellow-entrance 1s cubic-bezier(0.34, 1.56, 0.64, 1) 0.3s backwards;
}

.yellow-character.entrance-complete {
  animation: none;
}

@keyframes purple-entrance {
  0% {
    transform: translateX(-150px) translateY(50px) rotate(-15deg) scale(0.3);
    opacity: 0;
  }
  60% {
    transform: translateX(10px) translateY(-10px) rotate(3deg) scale(1.05);
  }
  100% {
    transform: translateX(0) translateY(0) rotate(0deg) scale(1);
    opacity: 1;
  }
}

@keyframes black-entrance {
  0% {
    transform: translateY(-100px) scale(0.5);
    opacity: 0;
  }
  70% {
    transform: translateY(10px) scale(1.08);
  }
  100% {
    transform: translateY(0) scale(1);
    opacity: 1;
  }
}

@keyframes orange-entrance {
  0% {
    transform: translateX(-200px) translateY(80px) rotate(-25deg) scale(0.2);
    opacity: 0;
  }
  65% {
    transform: translateX(15px) translateY(-8px) rotate(5deg) scale(1.1);
  }
  100% {
    transform: translateX(0) translateY(0) rotate(0deg) scale(1);
    opacity: 1;
  }
}

@keyframes yellow-entrance {
  0% {
    transform: translateX(200px) translateY(60px) rotate(20deg) scale(0.3);
    opacity: 0;
  }
  65% {
    transform: translateX(-12px) translateY(-5px) rotate(-4deg) scale(1.06);
  }
  100% {
    transform: translateX(0) translateY(0) rotate(0deg) scale(1);
    opacity: 1;
  }
}

.eyes {
  position: absolute;
  display: flex;
  transition: all 0.7s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: left, top;
}

.purple-character .eyes {
  gap: 32px;
}

.black-character .eyes {
  gap: 24px;
}

.orange-character .eyes {
  gap: 32px;
  transition: all 0.2s cubic-bezier(0, 0, 0.2, 1);
}

.yellow-character .eyes {
  gap: 24px;
  transition: all 0.2s cubic-bezier(0, 0, 0.2, 1);
}

.mouth {
  position: absolute;
  width: 80px;
  height: 4px;
  background-color: #2D2D2D;
  border-radius: 9999px;
  transition: all 0.2s cubic-bezier(0, 0, 0.2, 1);
}

.purple-mouth-shape {
  position: absolute;
  width: 24px;
  height: 8px;
  background-color: #2D2D2D;
  border-radius: 0 0 12px 12px;
  transition: left 0.7s cubic-bezier(0.4, 0, 0.2, 1), top 0.7s cubic-bezier(0.4, 0, 0.2, 1), width 0.5s cubic-bezier(0.4, 0, 0.2, 1), height 0.5s cubic-bezier(0.4, 0, 0.2, 1), border-radius 0.5s cubic-bezier(0.4, 0, 0.2, 1), transform 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.purple-mouth-shape--typing {
  width: 7px;
  height: 32px;
  border-radius: 0;
  transform: translateX(13.5px) translateY(-28px) var(--counter-skew, skewX(0deg));
}

.orange-mouth-shape {
  position: absolute;
  width: 26px;
  height: 13px;
  background-color: #2D2D2D;
  border-radius: 0 0 13px 13px;
  transition: left 0.2s cubic-bezier(0, 0, 0.2, 1), top 0.2s cubic-bezier(0, 0, 0.2, 1), width 0.5s cubic-bezier(0.4, 0, 0.2, 1), height 0.5s cubic-bezier(0.4, 0, 0.2, 1), border-radius 0.5s cubic-bezier(0.4, 0, 0.2, 1), transform 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.orange-mouth-shape--typing {
  width: 14px;
  height: 14px;
  border-radius: 50%;
  transform: translateX(6px);
}

/* Sad mouths */
.purple-mouth-shape--sad {
  width: 24px;
  height: 8px;
  border-radius: 12px 12px 0 0;
}

.orange-mouth-shape--sad {
  width: 26px;
  height: 13px;
  border-radius: 13px 13px 0 0;
}

.yellow-mouth-wrapper {
  position: absolute;
  transition: all 0.2s cubic-bezier(0, 0, 0.2, 1);
}

.yellow-mouth-path {
  d: path('M0 10 Q10 10, 20 10 Q30 10, 40 10 Q50 10, 60 10 Q70 10, 80 10');
  transition: d 0.5s cubic-bezier(0.4, 0, 0.2, 1);
}

.yellow-mouth-path--wavy {
  d: path('M0 10 Q10 2, 20 10 Q30 18, 40 10 Q50 2, 60 10 Q70 18, 80 10');
}

.yellow-mouth-path--happy {
  d: path('M0 2 Q10 10, 20 14 Q30 18, 40 18 Q50 18, 60 14 Q70 10, 80 2');
}

/* Happy mouths */
.purple-mouth-shape--happy {
  width: 30px;
  height: 16px;
  border-radius: 0 0 15px 15px;
}

.orange-mouth-shape--happy {
  width: 32px;
  height: 18px;
  border-radius: 0 0 16px 16px;
}

/* Confetti */
.confetti-container {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  overflow: visible;
  pointer-events: none;
  z-index: 10;
}

.confetti-piece {
  position: absolute;
  border-radius: 2px;
  animation: confetti-fall linear forwards;
}

@keyframes confetti-fall {
  0% {
    translate: 0 0;
    opacity: 1;
  }
  100% {
    translate: 30px 200vh;
    opacity: 1;
    rotate: 720deg;
  }
}
</style>
