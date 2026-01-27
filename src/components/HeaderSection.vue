<template>
  <header class="text-center mb-8 pt-8">
    <div class="flex items-center justify-center gap-4 flex-wrap">
      <h1 class="text-4xl md:text-5xl font-bold text-white/80 tracking-tight drop-shadow-lg">
        {{ title }}
      </h1>

      <div v-if="showClock" class="flex items-center gap-6 text-white/90">
        <div v-if="showClock && title" class="hidden md:block w-px h-12 bg-white/30"></div>

        <div class="flex flex-col items-start">
          <div class="text-3xl md:text-4xl font-mono font-light tracking-wider tabular-nums drop-shadow-md">
            {{ currentTime }}
          </div>
          <div v-if="showDate" class="text-sm text-white/70 font-medium tracking-wide mt-1">
            {{ currentDate }}
          </div>
        </div>
      </div>
    </div>
  </header>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from 'vue'
import dayjs from 'dayjs'

const props = defineProps({
  title: { type: String, default: '我的应用中心' },
  showClock: { type: Boolean, default: true },
  showDate: { type: Boolean, default: true }
})

const currentTime = ref('')
const currentDate = ref('')

let timer

const updateTime = () => {
  const now = dayjs()
  currentTime.value = now.format('HH:mm:ss')
  currentDate.value = now.format('MM月DD日 dddd')
}

onMounted(() => {
  updateTime()
  timer = setInterval(updateTime, 1000)
})

onUnmounted(() => {
  clearInterval(timer)
})
</script>