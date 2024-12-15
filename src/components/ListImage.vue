<script setup lang="ts">
import { ref, onMounted } from 'vue'
import axios from 'axios'

const images = ref([])

onMounted(async () => {
  try {
    const response = await axios.get(`${import.meta.env.VITE_API_URL}/list-images`)
    images.value = response.data
  } catch (error) {
    console.error('Error fetching images:', error)
  }
})
</script>

<template>
  <div class="images-list">
    <div v-for="image in images" :key="image.name" class="image-container">
      <img :src="image.data" :alt="image.name" class="image" />
    </div>
  </div>
</template>
