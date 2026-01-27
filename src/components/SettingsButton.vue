<template>
  <div class="fixed bottom-6 right-6 z-40 flex flex-col gap-3">
    <!-- 设置按钮 -->
    <button
      @click="$emit('open-settings')"
      class="w-12 h-12 rounded-full glass-card flex items-center justify-center text-white/70 hover:text-white hover:bg-white/20 transition-all shadow-lg"
      title="设置"
    >
      <Icon icon="mdi:cog" class="w-6 h-6" />
    </button>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { Icon } from '@iconify/vue'

defineEmits(['open-settings'])
</script>