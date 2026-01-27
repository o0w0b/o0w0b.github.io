<template>
  <div class="fixed inset-0 z-0 pointer-events-none overflow-hidden bg-background">
    <!-- 渐变底 -->
    <div
      class="absolute inset-0 bg-[radial-gradient(circle_at_20%_30%,rgba(184,159,129,0.4)_0%,transparent_45%),radial-gradient(circle_at_80%_20%,rgba(166,144,118,0.35)_0%,transparent_45%),radial-gradient(circle_at_50%_70%,rgba(230,220,207,0.3)_0%,transparent_45%)]" />

    <!-- 光斑 1：深咖啡（偏黄棕） -->
    <div class="absolute w-105 h-105 rounded-full top-[20%] left-[10%] bg-[#8b6b4a]/45 animate-float-breathe-1" />

    <!-- 光斑 2：深陶土（偏红棕） -->
    <div class="absolute w-130 h-130 rounded-full top-[10%] left-[70%] bg-[#a65d48]/40 animate-float-breathe-2" />

    <!-- 光斑 3：深驼褐（偏绿棕） -->
    <div class="absolute w-115 h-115 rounded-full top-[70%] left-[50%] bg-[#6d5a47]/45 animate-float-breathe-3" />
  </div>
</template>