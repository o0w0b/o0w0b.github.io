<template>
  <div class="glass-card p-4 mb-8 max-w-4xl mx-auto">
    <div class="flex flex-wrap items-center justify-center gap-6 md:gap-8 text-white/90">
      
      <!-- CPU 核心数 -->
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-blue-500/20 flex items-center justify-center">
          <Icon icon="mdi:cpu-64-bit" class="w-6 h-6 text-blue-400" />
        </div>
        <div>
          <div class="text-xs text-white/60">CPU 线程</div>
          <div class="text-sm font-semibold">{{ cpuCores }}</div>
        </div>
      </div>

      <!-- 内存 -->
      <div v-if="memorySupported" class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-purple-500/20 flex items-center justify-center">
          <Icon icon="mdi:memory" class="w-6 h-6 text-purple-400" />
        </div>
        <div>
          <div class="text-xs text-white/60">内存</div>
          <div class="text-sm font-semibold">{{ memory }} GB</div>
        </div>
      </div>

      <!-- 电池状态（仅支持时显示） -->
      <div v-if="batterySupported" class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-green-500/20 flex items-center justify-center">
          <Icon :icon="batteryIcon" class="w-6 h-6" :class="batteryColor" />
        </div>
        <div>
          <div class="text-xs text-white/60">电池</div>
          <div class="text-sm font-semibold">
            {{ batteryLevel }}%
            <span v-if="batteryCharging" class="text-xs text-green-400 ml-1">⚡</span>
          </div>
        </div>
      </div>

      <!-- 设备类型 -->
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-green-500/20 flex items-center justify-center">
          <Icon icon="mdi:desktop-tower" class="w-6 h-6 text-green-400" />
        </div>
        <div>
          <div class="text-xs text-white/60">设备</div>
          <div class="text-sm font-semibold">{{ deviceType }}</div>
        </div>
      </div>

      <!-- 屏幕分辨率 -->
      <div class="flex items-center gap-3">
        <div class="w-10 h-10 rounded-xl bg-orange-500/20 flex items-center justify-center">
          <Icon icon="mdi:monitor" class="w-6 h-6 text-orange-400" />
        </div>
        <div>
          <div class="text-xs text-white/60">分辨率</div>
          <div class="text-sm font-semibold">{{ screenSize }}</div>
        </div>
      </div>

    </div>
  </div>
</template>

<script setup>
import { ref, onMounted, onUnmounted, computed } from 'vue'
import { Icon } from '@iconify/vue'

// CPU 信息
const cpuCores = ref(navigator.hardwareConcurrency || '未知')

// 内存信息
const memory = ref('-')
const memorySupported = ref(false)
try {
  if (navigator.deviceMemory) {
    memorySupported.value = true
    const mem = navigator.deviceMemory
    if (mem >= 8) {
      memory.value = '8+'
    } else {
      memory.value = String(mem)
    }
  } else {
    memorySupported.value = false
    memory.value = '-'
  }
} catch (e) {
  console.log('无法读取内存信息')
  memorySupported.value = false
}

// 设备类型检测
const deviceType = ref('-')
const detectDeviceType = () => {
  const ua = navigator.userAgent.toLowerCase()
  if (/(tablet|ipad|playbook|silk)|(android(?!.*mobi))/i.test(ua)) {
    deviceType.value = '平板'
  } else if (/mobile|android|iphone|ipad|ipod|windows phone/i.test(ua)) {
    deviceType.value = '手机'
  } else {
    deviceType.value = '电脑'
  }
}

// 屏幕分辨率
const screenSize = ref(`${window.screen.width}×${window.screen.height}`)

// 电池状态
const batterySupported = ref(false)
const batteryLevel = ref(100)
const batteryCharging = ref(false)
let batteryRef = null

const batteryIcon = computed(() => {
  const level = batteryLevel.value
  if (batteryCharging.value) return 'mdi:battery-charging'
  if (level <= 10) return 'mdi:battery-alert'
  if (level <= 20) return 'mdi:battery-20'
  if (level <= 40) return 'mdi:battery-40'
  if (level <= 60) return 'mdi:battery-60'
  if (level <= 80) return 'mdi:battery-80'
  return 'mdi:battery'
})

const batteryColor = computed(() => {
  if (batteryCharging.value) return 'text-green-400'
  if (batteryLevel.value <= 20) return 'text-red-400'
  if (batteryLevel.value <= 50) return 'text-yellow-400'
  return 'text-green-400'
})

const updateBatteryInfo = (battery) => {
  batteryLevel.value = Math.round(battery.level * 100)
  batteryCharging.value = battery.charging
}

const initBattery = async () => {
  try {
    if ('getBattery' in navigator) {
      batteryRef = await navigator.getBattery()
      batterySupported.value = true
      updateBatteryInfo(batteryRef)
      
      batteryRef.addEventListener('levelchange', () => updateBatteryInfo(batteryRef))
      batteryRef.addEventListener('chargingchange', () => updateBatteryInfo(batteryRef))
    }
  } catch (e) {
    batterySupported.value = false
  }
}

// 监听变化
const handleResize = () => {
  screenSize.value = `${window.screen.width}×${window.screen.height}`
}

onMounted(() => {
  detectDeviceType()
  initBattery()
  
  window.addEventListener('resize', handleResize)
})

onUnmounted(() => {
  window.removeEventListener('resize', handleResize)
  
  if (batteryRef) {
    batteryRef.removeEventListener('levelchange', updateBatteryInfo)
    batteryRef.removeEventListener('chargingchange', updateBatteryInfo)
  }
})
</script>