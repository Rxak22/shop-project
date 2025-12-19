<script setup lang="ts">
import axios from 'axios'
import { ref, onBeforeMount, watch, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'

import SuggestCard from './suggestCard.vue'
import LoadingLog from './loadingLog.vue'
import { useHead } from '@vueuse/head'
import { useCart } from '@/stores/cart'

const route = useRoute()
const { addItem } = useCart()

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

const props = defineProps<{
  pid: string
}>()

const loadingDetail = ref(false)
const loadingSuggest = ref(false)

const suggestData = ref<Product[]>([])
const data = ref<Product | null>(null)
onBeforeMount(async () => {
  try {
    loadingDetail.value = true
    loadingSuggest.value = true
    const res = await axios.get<Product>(`https://api.escuelajs.co/api/v1/products/${props.pid}`)
    data.value = res.data
  } catch (err) {
    console.log('Fetching Error...', err)
  } finally {
    loadingDetail.value = false
  }

  try {
    // get suggest data by item category id
    const catId = data.value?.category.id
    const suggestRes = await axios.get<Product[]>(
      `https://api.escuelajs.co/api/v1/products/?offset=0&limit=20&categoryId=${catId}`,
    )
    suggestData.value = suggestRes.data
  } catch (err) {
    console.log('Fetching Error...', err)
  } finally {
    loadingSuggest.value = false
  }
})

const fetchProduct = async (id: number) => {
  try {
    loadingDetail.value = true
    const res = await axios.get<Product>(`https://api.escuelajs.co/api/v1/products/${id}`)
    data.value = res.data
  } catch (err) {
    console.log('Fetching Error...', err)
  } finally {
    loadingDetail.value = false
  }
}

watch(
  () => route.params.pid,
  (newId) => {
    fetchProduct(Number(newId))
  },
)

const router = useRouter()
const handleOpenProductDetail = (id: number, pid: number) => {
  router.push({ name: 'details', params: { id: id, pid: pid } })
}

useHead({
  title: computed(() => data.value?.category.name ?? 'Loading...'),
})

const handleAddToCart = () => {
  if (data.value) {
    addItem({
      id: data.value.id,
      name: data.value.title,
      price: data.value.price,
      image: data.value.images,
    })
    alert('Added to cart!')
  }
}
</script>

<template>
  <div class="container mx-auto mt-3 flex flex-col md:flex-row gap-6">
    <div class="w-full md:w-2/3 relative">
      <LoadingLog :loading="loadingDetail" />
      <h1 class="text-2xl md:text-4xl font-bold">{{ data?.title }}</h1>
      <img class="w-full h-64 md:h-[500px] mt-5 object-cover" :src="data?.images" :alt="data?.title" loading="lazy"
        decoding="async" />
      <h2 class="text-xl md:text-2xl font-semibold mt-3 text-amber-500">$ {{ data?.price }}</h2>
      <p class="mt-2 text-sm md:text-base">{{ data?.description }}</p>

      <div class="mt-4 flex flex-col sm:flex-row gap-2">
        <button @click="handleAddToCart"
          class="px-3 py-2 bg-amber-500 rounded-sm text-slate-50 cursor-pointer hover:bg-amber-600 transition">
          Add to Cart
        </button>
        <button class="px-3 py-2 bg-amber-600 rounded-sm text-slate-50 cursor-pointer">
          Buy Now
        </button>
      </div>
    </div>

    <aside class="w-full md:w-1/3 h-auto">
      <h1 class="text-xl md:text-2xl text-slate-500 font-semibold">Recommended Products</h1>
      <div class="w-full grid grid-cols-2 sm:grid-cols-2 md:grid-cols-2 gap-2 mt-2 relative"
        :class="loadingSuggest ? 'h-[85vh]' : 'h-auto'">
        <LoadingLog :loading="loadingSuggest" />
        <SuggestCard v-for="(product, index) in suggestData" :key="index" :product="product"
          @open-detail="handleOpenProductDetail(product.category.id, product.id)" />
      </div>
    </aside>
  </div>
</template>
