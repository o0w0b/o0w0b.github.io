<template>
  <div class="relative min-h-screen w-full overflow-hidden">
    <!-- 有壁纸时显示壁纸 -->
    <div v-if="wallpaper" class="fixed inset-0 z-0 bg-cover bg-center"
      :style="{ backgroundImage: `url(${wallpaper})` }">
      <div class="absolute inset-0 bg-linear-to-b from-black/80 via-black/40 to-black/60"></div>
    </div>

    <!-- 无壁纸时显示默认背景 -->
    <div v-else>
      <Background />
      <!-- <div class="absolute inset-0 bg-linear-to-b from-black/40 via-black/0 to-black/20"></div> -->
    </div>

    <!-- 主内容 -->
    <main class="relative z-10 container mx-auto px-4 py-8 min-h-screen flex flex-col">
      <!-- 头部：标题 + 时钟 -->
      <HeaderSection :title="siteTitle" :show-clock="showClock" :show-date="showDate" />

      <!-- 系统状态监控 -->
      <SystemStatus v-if="showSystemStatus" />

      <!-- 搜索框 -->
      <SearchBar v-if="showSearch" :search-engines="searchEngines" :default-engine="currentEngine"
        @search="handleSearch" />

      <!-- 应用分组列表 -->
      <div class="flex-1 space-y-8 mt-8">
        <AppGroup v-for="group in appGroups" :key="group.id" :group="group" :context-menu="activeContextMenu"
          @show-context="handleContextMenu" @open-app="openApp" />
      </div>

      <!-- 底部信息 -->
      <footer class="text-center py-8">
        <div
          class="inline-flex items-center gap-5 px-5 py-2.5 rounded-xl bg-white/5 border border-white/10 backdrop-blur-xl">
          <div class="flex items-center gap-2 text-black/80 text-sm">
            <Icon icon="mdi:copyright" class="w-4 h-4 text-white/60" />
            <span>{{ currentYear }} </span>
            <a href="https://github.com/o0w0b " target="_blank" class="hover:text-blue-400 transition-colors">
              o0w0b
            </a>
          </div>
          <div class="w-px h-4 bg-white/20"></div>
          <a href="https://beian.miit.gov.cn/ " target="_blank"
            class="group flex items-center gap-2 text-black/80 text-sm hover:text-blue-400 transition-colors">
            <Icon icon="mdi:shield-check" class="w-4 h-4 text-green-400/60" />
            吉ICP备2022002933号-1
          </a>
        </div>
      </footer>
    </main>

    <!-- 悬浮设置按钮 -->
    <SettingsButton @open-settings="showSettings = true" />

    <!-- 设置面板 -->
    <SettingsPanel v-if="config" v-model:visible="showSettings" :config="config" :app-groups="appGroups"
      @toggle-clock="showClock = !showClock" @toggle-date="showDate = !showDate"
      @toggle-search="showSearch = !showSearch" @toggle-system-status="showSystemStatus = !showSystemStatus" />
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue'
import yaml from 'js-yaml'
import HeaderSection from './components/HeaderSection.vue'
import SearchBar from './components/SearchBar.vue'
import SystemStatus from './components/SystemStatus.vue'
import AppGroup from './components/AppGroup.vue'
import SettingsButton from './components/SettingsButton.vue'
import SettingsPanel from './components/SettingsPanel.vue'
import Background from './components/Background.vue'
import { Icon } from '@iconify/vue'
import dayjs from 'dayjs'

const currentYear = dayjs().year()

const siteTitle = ref('我的应用中心')
const showClock = ref(true)
const showDate = ref(true)
const showSearch = ref(true)
const showSystemStatus = ref(true)
const wallpaper = ref('')
const showSettings = ref(false)
const config = ref(null)
const searchEngines = ref({
  google: {
    name: "Google",
    url: "https://www.google.com/search?q= {query}",
    icon: "logos:google-icon"
  }
})
const currentEngine = ref(searchEngines.value.google)
const appGroups = ref([])

// 全局右键菜单状态
const activeContextMenu = ref({
  app: null,
  pos: { x: 0, y: 0 }
})

const handleContextMenu = (app, pos) => {
  activeContextMenu.value = { app, pos }
}

const loadConfig = async () => {
  try {
    const response = await fetch('/config.yaml')
    const yamlText = await response.text()
    const loadedConfig = yaml.load(yamlText)

    siteTitle.value = loadedConfig.title || '我的应用中心'
    showClock.value = loadedConfig.showClock !== false
    showDate.value = loadedConfig.showDate !== false
    showSearch.value = loadedConfig.showSearch !== false
    showSystemStatus.value = loadedConfig.showSystemStatus !== false
    wallpaper.value = loadedConfig.wallpaper || ''

    config.value = {
      title: siteTitle.value,
      showClock: showClock.value,
      showDate: showDate.value,
      showSearch: showSearch.value,
      showSystemStatus: showSystemStatus.value,
      wallpaper: wallpaper.value
    }

    if (loadedConfig.searchEngines) {
      searchEngines.value = { ...searchEngines.value, ...loadedConfig.searchEngines }
    }
    const defaultEngineKey = loadedConfig.defaultEngine || 'google'
    currentEngine.value = searchEngines.value[defaultEngineKey] || searchEngines.value.google

    appGroups.value = loadedConfig.appGroups || []

  } catch (error) {
    console.error('加载配置文件失败:', error)
    appGroups.value = []
  }
}

onMounted(async () => {
  await loadConfig()
  // 点击页面其他地方关闭右键菜单
  document.addEventListener('click', () => {
    activeContextMenu.value.app = null
  })
})

const handleSearch = (query, engine, openInNew = true) => {
  if (!query.trim()) return
  const engineConfig = searchEngines.value[engine]
  if (engineConfig) {
    const url = engineConfig.url.replace('{query}', encodeURIComponent(query))
    if (openInNew) {
      window.open(url, '_blank')
    } else {
      window.location.href = url
    }
  }
}

const openApp = (app) => {
  if (app.openInNew !== false) {
    window.open(app.url, '_blank')
  } else {
    window.location.href = app.url
  }
}
</script>