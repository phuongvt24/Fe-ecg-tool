<script setup lang="ts">
import HeaderHome from './components/HeaderHome.vue'
import InputFile from './components/InputFile.vue'
import FooterHome from './components/FooterHome.vue'
</script>

<template>
  <div class="w-[100vw] min-h-[100vh] bg-slate-50">
    <HeaderHome class="sticky top-0 z-10" />
    <div class="w-full" style="min-height: calc(100vh - 18rem)">
      <div class="w-full flex justify-evenly">
        <InputFile />
      </div>
    </div>
    <FooterHome class="mt-20" />
  </div>
</template>
