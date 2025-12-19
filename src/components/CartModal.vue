<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'
import { useCart } from '@/stores/cart'

const props = defineProps<{
    isOpen: boolean
}>()

const emit = defineEmits<{
    close: []
}>()

const { items, totalPrice, removeItem, updateQuantity, clearCart } = useCart()

const modalPanel = ref<HTMLElement | null>(null)
const previouslyFocused = ref<HTMLElement | null>(null)

const closeModal = () => {
    emit('close')
    if (previouslyFocused.value) previouslyFocused.value.focus()
}

const handleKey = (e: KeyboardEvent) => {
    if (e.key === 'Escape') {
        closeModal()
    }
}

watch(() => props.isOpen, async (val) => {
    if (val) {
        previouslyFocused.value = document.activeElement as HTMLElement
        await nextTick()
        modalPanel.value?.focus()
        document.body.style.overflow = 'hidden'
        window.addEventListener('keydown', handleKey)
    } else {
        document.body.style.overflow = ''
        window.removeEventListener('keydown', handleKey)
    }
})

const handleCheckout = () => {
    if (items.value.length > 0) {
        alert('Proceeding to checkout with ' + items.value.length + ' item(s)')
        // You can add actual checkout logic here
    }
}
</script>

<template>
    <div v-if="isOpen" class="fixed inset-0 z-50 flex items-center justify-center">
        <!-- Backdrop -->
        <div class="absolute inset-0 bg-black opacity-50" @click="emit('close')"></div>

        <!-- Modal -->
        <div ref="modalPanel" tabindex="-1" role="dialog" aria-modal="true"
            class="relative bg-white rounded-none sm:rounded-lg shadow-lg w-full h-full sm:max-w-md sm:max-h-screen flex flex-col">
            <!-- Header -->
            <div class="flex justify-between items-center p-4 border-b">
                <h2 class="text-xl font-semibold">Shopping Cart</h2>
                <button @click="closeModal" aria-label="Close cart"
                    class="text-gray-500 hover:text-gray-700 p-1 focus:outline-none focus:ring-2 focus:ring-amber-400 rounded">
                    <i class="fa-solid fa-times text-2xl"></i>
                </button>
            </div>

            <!-- Items -->
            <div class="flex-1 overflow-y-auto p-4">
                <div v-if="items.length === 0" class="text-center text-gray-500 py-8">
                    <i class="fa-solid fa-shopping-cart text-4xl mb-4 block opacity-50"></i>
                    <p>Your cart is empty</p>
                </div>

                <div v-else class="space-y-4">
                    <div v-for="item in items" :key="item.id" class="flex gap-3 border-b pb-4">
                        <!-- Image -->
                        <img :src="item.image" :alt="item.name" class="w-16 h-16 object-cover rounded" loading="lazy"
                            decoding="async" />

                        <!-- Details -->
                        <div class="flex-1">
                            <h3 class="font-semibold text-sm">{{ item.name }}</h3>
                            <p class="text-amber-600 font-semibold">${{ item.price.toFixed(2) }}</p>

                            <!-- Quantity Control -->
                            <div class="flex items-center gap-2 mt-2">
                                <button @click="updateQuantity(item.id, item.quantity - 1)"
                                    class="bg-gray-200 hover:bg-gray-300 px-2 py-1 rounded text-sm">
                                    -
                                </button>
                                <span class="w-6 text-center">{{ item.quantity }}</span>
                                <button @click="updateQuantity(item.id, item.quantity + 1)"
                                    class="bg-gray-200 hover:bg-gray-300 px-2 py-1 rounded text-sm">
                                    +
                                </button>
                                <button @click="removeItem(item.id)"
                                    class="ml-auto text-red-500 hover:text-red-700 text-sm">
                                    Remove
                                </button>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Footer -->
            <div v-if="items.length > 0" class="border-t p-4 space-y-3">
                <div class="flex justify-between font-semibold text-lg">
                    <span>Total:</span>
                    <span class="text-amber-600">${{ totalPrice }}</span>
                </div>
                <button @click="handleCheckout"
                    class="w-full bg-amber-500 hover:bg-amber-600 text-white font-semibold py-2 rounded">
                    Checkout
                </button>
                <button @click="clearCart"
                    class="w-full bg-gray-200 hover:bg-gray-300 text-gray-800 font-semibold py-2 rounded">
                    Clear Cart
                </button>
            </div>
        </div>
    </div>
</template>
