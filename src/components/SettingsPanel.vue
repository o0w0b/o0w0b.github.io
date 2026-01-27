<template>
  <Transition name="slide">
    <div v-if="visible" class="fixed inset-0 z-50 flex justify-end">
      <!-- 遮罩 -->
      <div class="absolute inset-0 bg-black/50 backdrop-blur-sm" @click="close"></div>
      
      <!-- 面板 -->
      <div class="relative w-full max-w-md h-full bg-slate-900/95 backdrop-blur-xl border-l border-white/10 shadow-2xl overflow-y-auto">
        <div class="p-6">
          <!-- 头部 -->
          <div class="flex items-center justify-between mb-8">
            <h2 class="text-2xl font-bold text-white">设置</h2>
            <button 
              @click="close"
              class="p-2 rounded-lg hover:bg-white/10 text-white/60 hover:text-white transition-colors"
            >
              <Icon icon="mdi:close" class="w-6 h-6" />
            </button>
          </div>

          <!-- 基本设置 -->
          <div class="space-y-6">
            <section>
              <h3 class="text-sm font-semibold text-white/50 uppercase tracking-wider mb-4">基本设置</h3>
              
              <div class="space-y-4">
                <div>
                  <label class="block text-white/80 text-sm mb-2">站点标题</label>
                  <div class="w-full px-4 py-2 bg-white/5 border border-white/10 rounded-lg text-white/60">
                    {{ config.title || '我的应用中心' }}
                  </div>
                  <p class="text-xs text-white/40 mt-1">此设置通过 config.yaml 配置</p>
                </div>

                <div class="flex items-center justify-between">
                  <span class="text-white/80">显示时钟</span>
                  <button 
                    @click="toggle('showClock')"
                    class="w-12 h-6 rounded-full transition-colors relative"
                    :class="localConfig.showClock ? 'bg-blue-500' : 'bg-white/20'"
                  >
                    <div 
                      class="absolute top-1 w-4 h-4 rounded-full bg-white transition-transform"
                      :class="localConfig.showClock ? 'translate-x-7' : 'translate-x-1'"
                    ></div>
                  </button>
                </div>

                <div class="flex items-center justify-between">
                  <span class="text-white/80">显示日期</span>
                  <button 
                    @click="toggle('showDate')"
                    class="w-12 h-6 rounded-full transition-colors relative"
                    :class="localConfig.showDate ? 'bg-blue-500' : 'bg-white/20'"
                  >
                    <div 
                      class="absolute top-1 w-4 h-4 rounded-full bg-white transition-transform"
                      :class="localConfig.showDate ? 'translate-x-7' : 'translate-x-1'"
                    ></div>
                  </button>
                </div>

                <div class="flex items-center justify-between">
                  <span class="text-white/80">显示搜索</span>
                  <button 
                    @click="toggle('showSearch')"
                    class="w-12 h-6 rounded-full transition-colors relative"
                    :class="localConfig.showSearch ? 'bg-blue-500' : 'bg-white/20'"
                  >
                    <div 
                      class="absolute top-1 w-4 h-4 rounded-full bg-white transition-transform"
                      :class="localConfig.showSearch ? 'translate-x-7' : 'translate-x-1'"
                    ></div>
                  </button>
                </div>

                <div class="flex items-center justify-between">
                  <span class="text-white/80">显示系统状态</span>
                  <button 
                    @click="toggle('showSystemStatus')"
                    class="w-12 h-6 rounded-full transition-colors relative"
                    :class="localConfig.showSystemStatus ? 'bg-blue-500' : 'bg-white/20'"
                  >
                    <div 
                      class="absolute top-1 w-4 h-4 rounded-full bg-white transition-transform"
                      :class="localConfig.showSystemStatus ? 'translate-x-7' : 'translate-x-1'"
                    ></div>
                  </button>
                </div>
              </div>
            </section>

            <!-- 分组信息 -->
            <section>
              <h3 class="text-sm font-semibold text-white/50 uppercase tracking-wider mb-4">分组信息</h3>
              
              <div class="space-y-3">
                <div 
                  v-for="group in appGroups"
                  :key="group.id"
                  class="p-3 bg-white/5 rounded-lg"
                >
                  <div class="flex items-center gap-2 mb-2">
                    <Icon :icon="group.icon || 'mdi:folder'" class="w-4 h-4 text-white/60" />
                    <span class="text-white/80 font-medium">{{ group.name }}</span>
                    <span class="text-xs text-white/40 bg-white/10 px-2 py-0.5 rounded-full">
                      {{ group.apps?.length || 0 }} 个应用
                    </span>
                  </div>
                  <div v-if="group.apps?.length" class="text-sm text-white/60">
                    包含应用：{{ group.apps.map(app => app.title).join('、') }}
                  </div>
                </div>
              </div>
            </section>

            <!-- 配置提示 -->
            <section>
              <h3 class="text-sm font-semibold text-white/50 uppercase tracking-wider mb-4">配置说明</h3>
              <div class="space-y-3">
                <div class="p-4 bg-blue-500/10 border border-blue-500/20 rounded-lg">
                  <p class="text-sm text-blue-200 mb-2">
                    <strong>临时设置：</strong> 上方的开关可实时切换显示状态，仅在当前会话有效。
                  </p>
                  <p class="text-sm text-blue-200">
                    <strong>永久配置：</strong> 所有设置都通过 <code class="bg-blue-500/20 px-1 py-0.5 rounded text-xs">config.yaml</code> 文件配置。
                    编辑该文件后刷新页面即可应用更改。
                  </p>
                </div>
              </div>
            </section>
          </div>
        </div>
      </div>
    </div>
  </Transition>
</template>

<script setup>
import { ref, watch } from 'vue'
import { Icon } from '@iconify/vue'

const props = defineProps({
  visible: Boolean,
  config: Object,
  appGroups: Array
})

const emit = defineEmits(['update:visible', 'toggle-clock', 'toggle-date', 'toggle-search', 'toggle-system-status'])

const localConfig = ref({
  showClock: false,
  showDate: false,
  showSearch: false,
  showSystemStatus: false
})

watch(() => props.config, (newVal) => {
  if (newVal) {
    localConfig.value = {
      showClock: newVal.showClock ?? false,
      showDate: newVal.showDate ?? false,
      showSearch: newVal.showSearch ?? false,
      showSystemStatus: newVal.showSystemStatus ?? false
    }
  }
}, { immediate: true })

const toggle = (key) => {
  localConfig.value[key] = !localConfig.value[key]
  
  // 映射到对应的事件名
  const eventMap = {
    showClock: 'toggle-clock',
    showDate: 'toggle-date',
    showSearch: 'toggle-search',
    showSystemStatus: 'toggle-system-status'
  }
  
  emit(eventMap[key])
}

const close = () => {
  emit('update:visible', false)
}
</script>

<style scoped>
.slide-enter-active,
.slide-leave-active {
  transition: transform 0.3s ease;
}

.slide-enter-from,
.slide-leave-to {
  transform: translateX(100%);
}
</style>