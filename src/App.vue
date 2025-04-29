<script setup>
import { ref, onMounted, computed } from 'vue'
import { Analytics } from '@vercel/analytics/vue';

const wheelSize = 500 // px，調整這個數值即可改變 wheel 長寬

const wheelState = ref({
  spinning: false,
  rotation: 90,
  transition: 'none'
})
const counter = ref(0)
const formattedCounter = computed(() => counter.value.toLocaleString())
// 轉動秒數直接在程式內設定
const spinDuration = 1 // 單位: 秒
onMounted(()=>{
  // 讀取 localStorage 的 clickCount
  const clickCount = localStorage.getItem('clickCount')
  if (clickCount) {
    counter.value = parseInt(clickCount)
  } else {
    counter.value = 0
  }
})

function spinWheel() {
  counter.value++
  localStorage.setItem('clickCount', counter.value)



  // 隨機角度 (3~5圈, 即 1080~1800度)
  const minAngle = 3 * 360 // 1080
  const maxAngle = 5 * 360 // 1800
  let randomAngle = Math.floor(Math.random() * (maxAngle - minAngle + 1)) + minAngle

  let target = wheelState.value.rotation + randomAngle
  if(target%180  === 90) {
    // 讓轉動的時候不會剛好停在中間 
    target += Math.random() < 0.5 ? 10 : -10
  }
  
  


  // 更快停止的 cubic-bezier
  wheelState.value.transition = `transform ${spinDuration}s cubic-bezier(.33,1,.95,1)`
  wheelState.value.rotation = target

  setTimeout(() => {
    // 停止時 transition 設為 none，rotation 設為最終角度
    wheelState.value.transition = 'none'
    wheelState.value.spinning = false
    // 將 rotation 設為 randomAngle % 360，讓下次從這個角度開始
    wheelState.value.rotation = target % 360
  }, spinDuration * 1000)
}

</script>

<template>
  <Analytics />
  <div class="container">
    <div class="counter">click: {{formattedCounter}}</div>
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
        <polygon points="24,0 56,90 -8,90" fill="#222"/>
      </svg>
      <button class="pointer" @click="spinWheel" ></button>
    </div>
  </div>
</template>

<style scoped>
.container {
  position:relative;
  min-height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  background: #f6f6f6;

}
.counter {
  position: absolute;
  top: 4px;
  right: 4px;
  font-weight: bold;
  color: #222;
  user-select: none;
}
.wheel-container {
  position: relative;
  width: min(90vw, 500px);
  height: min(90vw, 500px);
  display: flex;
  align-items: center;
  justify-content: center;
}
.wheel {
  width: 100%;
  height: 100%;
  border-radius: 50%;
  position: relative;
  background: transparent;
  overflow: hidden;
  border: 3px solid #222; /* 新增黑邊 */
  /* 新增浮起來的陰影 */
  box-shadow: 0 16px 48px 0 #0003, 0 2px 8px #0002;
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
  background: #43a047;
  top: 0;
}
.dont {
  background: #e53935;
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
  width: 84px;
  height: 84px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  z-index: 4; /* 比 svg 高 */
  box-shadow: 0 2px 8px #0002;
  transition: background 0.2s;
  overflow: visible;
  padding: 0;
  user-select: none;
}
.pointer:active {
  background: #eee;
}
.pointer:disabled {
  cursor: not-allowed;
}

/* 移除原本的 max-width/max-height RWD，統一用 min() 控制 */
</style>
