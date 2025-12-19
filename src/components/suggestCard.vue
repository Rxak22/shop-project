<script setup lang="ts">
interface Products {
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
defineProps<{
  product: Products
}>()

const emit = defineEmits<{
  (e: 'openDetail', id: number): void
}>()
</script>

<template>
  <div
    class="w-full shadow-md cursor-pointer p-3 rounded-lg focus:outline-none focus:ring-2 focus:ring-amber-400 hover:shadow-lg transition"
    @click="emit('openDetail', product.id)" role="button" tabindex="0" @keyup.enter="emit('openDetail', product.id)">
    <img class="w-full h-48 object-cover rounded-md" :src="product.images" :alt="product.title" loading="lazy"
      decoding="async" />
    <p class="text-base mt-3 line-clamp-2 font-medium">{{ product.title }}</p>
    <p class="text-red-500 text-lg font-semibold mt-2">${{ product.price }}</p>
  </div>
</template>
