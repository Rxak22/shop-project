<script setup lang="ts">
import { defineProps } from 'vue'
import { useRouter } from 'vue-router'

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
  products: Product[]
}>()

const router = useRouter()
const handleOpenProductDetail = (id: number, pid: number) => {
  router.push({ name: 'details', params: { id: id, pid: pid } })
} 
</script>

<template>
  <div class="container mx-auto grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 lg:grid-cols-4 gap-4 mt-4">
    <div
      class="shadow-md shadow-gray-400 hover:shadow-gray-600 rounded overflow-hidden cursor-pointer focus:outline-none focus:ring-2 focus:ring-amber-400"
      v-for="(item, index) in props.products" @click="handleOpenProductDetail(item.category.id, item.id)" :key="index"
      role="button" tabindex="0" @keyup.enter="handleOpenProductDetail(item.category.id, item.id)">
      <img class="object-cover w-full h-48 md:h-60" :src="item.images" :alt="item.title" loading="lazy"
        decoding="async" />
      <div class="p-3 bg-white">
        <h4 class="text-base md:text-lg font-semibold line-clamp-2">{{ item.title }}</h4>
        <p class="text-sm line-clamp-2 text-slate-600 mt-1">{{ item.description }}</p>
        <div class="mt-3 text-right">
          <span class="text-red-500 font-semibold">${{ item.price }}</span>
        </div>
      </div>
    </div>
  </div>
</template>
