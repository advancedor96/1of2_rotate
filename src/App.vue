<script setup>
import { ref } from 'vue'

const wheelSize = 500 // px，調整這個數值即可改變 wheel 長寬

const wheelState = ref({
  spinning: false,
  rotation: 0,
  transition: 'none'
})

// 轉動秒數直接在程式內設定
const spinDuration = 3 // 單位: 秒

function spinWheel() {
  if (wheelState.value.spinning) return
  wheelState.value.spinning = true

  // 隨機圈數(3~8圈)
  const rounds = Math.floor(Math.random() * 6) + 3 // 3~8圈
  const end = Math.random() < 0.5 ? 0 : 180 // 0度(Do)或180度(Don't)
  const target = rounds * 360 + end

  // 慢啟動慢停止的 transition
  wheelState.value.transition = `transform ${spinDuration}s cubic-bezier(.3,1.5,.7,1)`
  wheelState.value.rotation = target

  setTimeout(() => {
    // 停止時 transition 設為 none，rotation 設為最終角度
    wheelState.value.transition = 'none'
    wheelState.value.spinning = false
    // wheelState.value.rotation = end
  }, spinDuration * 1000)
}
</script>

<template>
  <div class="container">
    <div
      class="wheel-container"
      :style="`--wheel-size: ${wheelSize}px;`"
    >
      <div
        class="wheel"
        :style="`transform: rotate(${wheelState.rotation}deg); transition: ${wheelState.transition};`"
      >
        <div class="half do">Do</div>
        <div class="half dont">Don't</div>
      </div>
      <!-- svg 指針移到 button 外面，並絕對定位 -->
      <svg class="pointer-svg" width="48" height="110" viewBox="0 0 48 110">
        <polygon points="24,2 46,90 2,90" fill="#222"/>
      </svg>
      <button class="pointer" @click="spinWheel" :disabled="wheelState.spinning"></button>
    </div>
  </div>
</template>

<style scoped>
.container {
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f6f6f6;
}
.wheel-container {
  position: relative;
  width: var(--wheel-size, 300px);
  height: var(--wheel-size, 300px);
  display: flex;
  align-items: center;
  justify-content: center;
}
.wheel {
  width: var(--wheel-size, 300px);
  height: var(--wheel-size, 300px);
  border-radius: 50%;
  position: relative;
  box-shadow: 0 4px 24px #0002;
  background: transparent;
  overflow: hidden;
}
.half {
  position: absolute;
  width: 100%;
  height: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 2.5rem;
  font-weight: bold;
  color: #fff;
  user-select: none;
  letter-spacing: 2px;
}
.do {
  background: #2ecc40;
  top: 0;
}
.dont {
  background: #ff4136;
  bottom: 0;
  transform: rotate(180deg);
}
.pointer-svg {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -70%);
  z-index: 3; /* 比 button 低 */
  pointer-events: none;
  /* 讓 svg 指針往上超出圓形更多 */
  width: 48px;
  height: 110px;
}
.pointer {
  position: absolute;
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  background: #fff;
  border: 4px solid #222;
  border-radius: 50%;
  width: 64px;
  height: 64px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 4; /* 比 svg 高 */
  box-shadow: 0 2px 8px #0002;
  transition: background 0.2s;
  overflow: visible;
  padding: 0;
}
.pointer:active {
  background: #eee;
}
.pointer:disabled {
  cursor: not-allowed;
}
@media (max-width: 400px) {
  .wheel-container {
    width: 90vw;
    height: 90vw;
    max-width: var(--wheel-size, 300px);
    max-height: var(--wheel-size, 300px);
  }
  .wheel {
    width: 90vw;
    height: 90vw;
    max-width: var(--wheel-size, 300px);
    max-height: var(--wheel-size, 300px);
  }
}
</style>
