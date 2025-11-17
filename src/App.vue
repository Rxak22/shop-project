<script setup lang="ts">
import { ref, onBeforeMount } from 'vue'
import axios from 'axios'

import NavHeader from './components/navHeader.vue'
import SiteFooter from './components/siteFooter.vue'

interface Product {
  id: number
  name: string
  images: string
}

const loading = ref(false)
const data = ref<Product[]>([])
onBeforeMount(async () => {
  try {
    loading.value = true;
    const response = await axios.get<Product[]>('https://api.escuelajs.co/api/v1/categories')
    data.value = response.data
  } catch (err) {
    console.error('Error Fetching Products', err)
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <header class="sticky left-0 top-0 z-50">
    <NavHeader :loading="loading" :categories="data" />
  </header>

  <main>
    <RouterView />
  </main>

  <SiteFooter />
</template>
