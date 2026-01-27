<template>
  <div class="w-full px-4 sm:px-8 md:px-12 lg:px-16 mb-8">
    <div class="relative group max-w-lg sm:max-w-2xl md:max-w-3xl lg:max-w-4xl mx-auto">
      <!-- 搜索引擎选择器 -->
      <div class="absolute left-4 top-1/2 -translate-y-1/2 z-20">
        <button 
          @click="showEngineMenu = !showEngineMenu"
          class="flex items-center gap-2 px-3 py-1.5 rounded-lg bg-white/10 hover:bg-white/20 transition-colors text-white/80"
        >
          <Icon :icon="currentEngine.icon" class="w-5 h-5" />
          <Icon icon="mdi:chevron-down" class="w-4 h-4" />
        </button>
        
        <!-- 搜索引擎下拉菜单 -->
        <div 
          v-if="showEngineMenu"
          class="absolute top-full left-0 mt-2 w-48 glass-card overflow-hidden z-30"
        >
          <button
            v-for="engine in Object.values(searchEngines)"
            :key="engine.name"
            @click="selectEngine(engine)"
            class="w-full flex items-center gap-3 px-4 py-3 hover:bg-white/10 transition-colors text-white/90 text-left"
          >
            <Icon :icon="engine.icon" class="w-5 h-5" />
            <span>{{ engine.name }}</span>
          </button>
          
          <!-- 分隔线 -->
          <div class="border-t border-white/10"></div>
          
          <!-- 新标签页选项 -->
          <div class="px-4 py-3 flex items-center gap-3 text-white/90">
            <button 
              @click="openInNewTab = !openInNewTab"
              class="w-4 h-4 rounded border border-white/30 flex items-center justify-center"
              :class="openInNewTab ? 'bg-blue-500 border-blue-500' : 'bg-transparent'"
            >
              <Icon 
                v-if="openInNewTab" 
                icon="mdi:check" 
                class="w-3 h-3 text-white" 
              />
            </button>
            <span class="text-sm">新标签页打开</span>
          </div>
        </div>
      </div>

      <!-- 搜索输入框 -->
      <input
        v-model="searchQuery"
        type="text"
        placeholder="输入搜索内容..."
        class="w-full pl-32 pr-14 py-4 bg-white/10 backdrop-blur-md border border-white/20 rounded-2xl text-white placeholder-white/50 focus:outline-none focus:ring-2 focus:ring-white/30 focus:bg-white/15 transition-all text-lg"
        @keyup.enter="handleSearch"
      />

      <!-- 搜索按钮 -->
      <button 
        @click="handleSearch"
        class="absolute right-4 top-1/2 -translate-y-1/2 p-2 rounded-lg hover:bg-white/10 transition-colors text-white/70 hover:text-white"
      >
        <Icon icon="mdi:magnify" class="w-6 h-6" />
      </button>
    </div>

    <!-- 点击外部关闭菜单 -->
    <div 
      v-if="showEngineMenu" 
      class="fixed inset-0 z-10" 
      @click="showEngineMenu = false"
    ></div>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  searchEngines: { type: Object, required: true },
  defaultEngine: { type: Object, required: true }
})

const emit = defineEmits(['search'])

const searchQuery = ref('')
const currentEngine = ref(props.defaultEngine)
const showEngineMenu = ref(false)
const openInNewTab = ref(true)  // 默认在新标签页打开

const selectEngine = (engine) => {
  currentEngine.value = engine
  showEngineMenu.value = false
}

const handleSearch = () => {
  if (searchQuery.value.trim()) {
    // 找到当前引擎的key
    const engineKey = Object.keys(props.searchEngines).find(key => 
      props.searchEngines[key] === currentEngine.value
    ) || 'google'
    emit('search', searchQuery.value, engineKey, openInNewTab.value)
    searchQuery.value = ''
  }
}
</script>