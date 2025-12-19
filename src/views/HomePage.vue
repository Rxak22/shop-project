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
  <div>
    <div v-if="loading"
      class="container mx-auto grid grid-cols-2 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 mt-4">
      <SkeletonCard v-for="n in 8" :key="n" />
    </div>

    <div v-else>
      <ItemCard :products="data" />
    </div>

    <LoadingLog :loading="loading" />
  </div>
</template>