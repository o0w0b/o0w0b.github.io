<template>
  <section class="mb-8">
    <!-- 分组标题 -->
    <div class="flex items-center gap-2 mb-4 px-2">
      <h2 class="text-lg font-semibold text-white/90">{{ group.name }}</h2>
      <span class="text-xs text-white/40 bg-white/10 px-2 py-0.5 rounded-full">
        {{ group.apps.length }}
      </span>
    </div>

    <!-- 应用网格 -->
    <div class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 xl:grid-cols-6 gap-4">
      <div v-for="app in group.apps" :key="app.id" class="app-card group" @click="$emit('open-app', app)"
        @contextmenu.prevent="handleContextMenu($event, app)">
        <!-- 图标 -->
        <div
          class="w-12 h-12 rounded-xl flex items-center justify-center text-2xl shrink-0 transition-transform group-hover:scale-110"
          :style="{ backgroundColor: app.color + '20', color: app.color }">
          <Icon :icon="app.icon" class="w-7 h-7" />
        </div>

        <!-- 信息 -->
        <div class="flex-1 min-w-0">
          <div class="text-white/90 font-medium truncate text-sm">{{ app.title }}</div>
          <div class="text-white/40 text-xs truncate">{{ getDomain(app.url) }}</div>
        </div>
      </div>
    </div>

    <!-- 描述浮窗 -->
    <div v-if="contextMenu.app?.description"
      class="fixed z-50 px-4 py-3 rounded-lg bg-slate-900/95 backdrop-blur-md border border-white/10 pointer-events-none max-w-xs"
      :style="tooltipStyle">
      <!-- 标题 + 描述 -->
      <div class="flex items-center gap-2 mb-1.5">
        <Icon :icon="contextMenu.app.icon" class="w-4 h-4" :style="{ color: contextMenu.app.color }" />
        <span class="text-white font-medium text-sm truncate">{{ contextMenu.app.title }}</span>
      </div>
      <p class="text-slate-300 text-xs leading-relaxed">{{ contextMenu.app.description }}</p>
    </div>
  </section>
</template>

<script setup>
import { computed } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  group: {
    type: Object,
    required: true
  },
  contextMenu: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['open-app', 'show-context'])

// 计算样式
const tooltipStyle = computed(() => {
  const pos = props.contextMenu.pos
  if (!pos) return {}  // 安全判断

  const padding = 16
  const tooltipWidth = 320
  const tooltipHeight = 100

  let x = pos.x + tooltipWidth / 4
  let y = pos.y - 12

  // 右边界检测
  if (x + tooltipWidth / 2 > window.innerWidth - padding) {
    x = window.innerWidth - tooltipWidth / 2 - padding
  }
  if (x - tooltipWidth / 2 < padding) {
    x = tooltipWidth / 2 + padding
  }
  if (y - tooltipHeight < padding) {
    y = pos.y + tooltipHeight + 12
  }

  return {
    left: x + 'px',
    top: y + 'px',
    transform: 'translate(-50%, -100%)'
  }
})

const getDomain = (url) => {
  try {
    return new URL(url).hostname
  } catch {
    return url
  }
}

const handleContextMenu = (e, app) => {
  e.preventDefault()
  e.stopPropagation()

  // 只通知父组件，由父组件更新 contextMenu
  emit('show-context', app, { x: e.clientX, y: e.clientY })
}
</script>