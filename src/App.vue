<script setup lang="ts">
import { ref, onBeforeMount } from 'vue'
import axios from 'axios'

import NavHeader from './components/navHeader.vue'
import SiteFooter from './components/siteFooter.vue'
import CartModal from './components/CartModal.vue'

interface Product {
  id: number
  name: string
  images: string
}

const loading = ref(false)
const data = ref<Product[]>([])
const isCartOpen = ref(false)

onBeforeMount(async () => {
  try {
    loading.value = true;
    const response = await axios.get<Product[]>('https://api.escuelajs.co/api/v1/categories?offset=0&limit=7')
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
    <NavHeader :loading="loading" :categories="data" @openCart="isCartOpen = true" />
  </header>

  <main>
    <RouterView />
  </main>

  <SiteFooter />

  <CartModal :isOpen="isCartOpen" @close="isCartOpen = false" />
</template>
