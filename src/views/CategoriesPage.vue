<script setup lang="ts">
import ItemCard from '@/components/itemCard.vue'
import LoadingLog from '@/components/loadingLog.vue'
import { useHead } from '@vueuse/head'
import axios from 'axios'
import { ref, onBeforeMount, watch, computed } from 'vue'
import { useRoute } from 'vue-router'

defineProps<{
  id: string
}>()

const route = useRoute()

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

const data = ref<Product[]>([])
const loading = ref(false)
onBeforeMount(() => {
  fetchProduct(Number(route.params.id))
})

const fetchProduct = async (id: number) => {
  try {
    loading.value = true
    const res = await axios.get<Product[]>(
      `https://api.escuelajs.co/api/v1/products/?categoryId=${id}`,
    )
    data.value = res.data
  } catch (err) {
    console.log('Fetching Error', err)
  } finally {
    loading.value = false
  }
}

watch(
  () => route.params.id,
  (newId) => {
    fetchProduct(Number(newId))
  },
)

useHead({
  title: computed(() => (data.value.length > 0 ? data.value[0].category.name : 'Loading...')),
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
