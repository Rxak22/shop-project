<script setup lang="ts">
import { ref, onBeforeMount } from 'vue'
import axios from 'axios'

import ItemCard from '@/components/itemCard.vue'
import LoadingLog from '@/components/loadingLog.vue'
import { useHead } from '@vueuse/head'

interface Product {
  id: number
  title: string
  price: number
  description: string
  images: string
  category: {
    id: number
    name: string
    image: string
  }
}
useHead({
  title: () => 'AllWays Store | Home',
})
const data = ref<Product[]>([])
const loading = ref(false)
onBeforeMount(async () => {
  try {
    loading.value = true
    const response = await axios.get<Product[]>(
      'https://api.escuelajs.co/api/v1/products?offset=0&limit=20',
    )
    data.value = response.data
  } catch (err) {
    console.error('Error Fetching Products', err)
  } finally {
    loading.value = false
  }
})
</script>

<template>
  <ItemCard :products="data" />
  <LoadingLog :loading="loading" />
</template>