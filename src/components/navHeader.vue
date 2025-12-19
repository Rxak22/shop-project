<script setup lang="ts">
import { ref, nextTick, watch } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useCart } from '@/stores/cart'

interface Categories {
    id: number
    name: string
    images: string
}
const props = defineProps<{
    categories: Categories[],
    loading: boolean
}>()

const emit = defineEmits<{
    openCart: []
}>()

const searchQuery = ref("")
const showMobileMenu = ref(false)

const route = useRoute()
const router = useRouter()
const { totalItems } = useCart()

const handleMobileSearch = () => {
    const q = prompt('Search for products:')
    if (!q || !q.trim()) return
    try {
        router.push({ name: 'search', query: { q } })
    } catch (err) {
        console.log('Search Error...', err)
    }
}

const isActiveCategory = (id: number) => {
    return route.params.id === String(id)
}

const mobileDrawer = ref<HTMLElement | null>(null)
const previouslyFocused = ref<HTMLElement | null>(null)

const closeMobileMenu = () => {
    showMobileMenu.value = false
    if (previouslyFocused.value) previouslyFocused.value.focus()
}

const handleEscKey = (e: KeyboardEvent) => {
    if (e.key === 'Escape') closeMobileMenu()

    if (e.key === 'Tab' && showMobileMenu.value) {
        const focusable = mobileDrawer.value?.querySelectorAll<HTMLElement>('a,button,input,select,textarea,[tabindex]:not([tabindex="-1"])')
        if (!focusable || focusable.length === 0) return
        const first = focusable[0]
        const last = focusable[focusable.length - 1]
        if (e.shiftKey) {
            if (document.activeElement === first) {
                last.focus()
                e.preventDefault()
            }
        } else {
            if (document.activeElement === last) {
                first.focus()
                e.preventDefault()
            }
        }
    }
}

watch(showMobileMenu, async (val) => {
    if (val) {
        previouslyFocused.value = document.activeElement as HTMLElement
        await nextTick()
        mobileDrawer.value?.focus()
        document.body.style.overflow = 'hidden'
        window.addEventListener('keydown', handleEscKey)
    } else {
        document.body.style.overflow = ''
        window.removeEventListener('keydown', handleEscKey)
    }
})

const handleSearchProduct = () => {
    if (!searchQuery.value.trim()) return

    try {
        router.push({ name: 'search', query: { q: searchQuery.value } })
    } catch (err) {
        console.log("Search Error...", err)
    }
}

</script>

<template>
    <div class="w-full mx-auto bg-slate-900">
        <div class="container mx-auto flex items-center justify-between px-4 py-2">
            <div class="flex items-center gap-2">
                <RouterLink class="block text-slate-50 p-2" :class="{ 'font-bold bg-green-900': route.path === '/' }"
                    to="/">
                    <i class="fa-solid fa-house"></i>
                </RouterLink>

                <!-- Mobile menu toggle -->
                <button @click="showMobileMenu = !showMobileMenu" :aria-expanded="showMobileMenu"
                    class="sm:hidden text-slate-50 p-2 focus:outline-none focus:ring-2 focus:ring-amber-400 rounded"
                    aria-label="Toggle menu">
                    <i class="fa-solid fa-bars"></i>
                </button>

                <!-- Desktop categories -->
                <ul class="hidden sm:flex text-slate-50 font-semibold gap-1">
                    <li v-if="props.loading">
                        <h1 class="p-4 text-slate-400">Loading...</h1>
                    </li>
                    <li v-else v-for="cat in props.categories" :key="cat.id">
                        <RouterLink class="block p-4 cursor-pointer"
                            :to="{ name: 'categories', params: { id: cat.id } }"
                            :class="{ 'bg-green-900': isActiveCategory(cat.id) }">
                            {{ cat.name }}
                        </RouterLink>
                    </li>
                </ul>
            </div>

            <div class="flex items-center gap-3">
                <!-- Search (hidden on small screens) -->
                <div v-if="!props.loading" class="hidden sm:flex items-center">
                    <input type="text" v-model="searchQuery" placeholder="Search..."
                        class="text-slate-300 bg-transparent outline-0 rounded-md text-sm border-b border-b-amber-600 mr-2 px-2 py-1"
                        @keyup.enter="handleSearchProduct" />
                    <i @click="handleSearchProduct"
                        class="fa-solid fa-magnifying-glass text-slate-300 cursor-pointer"></i>
                </div>

                <!-- Mobile Search Icon -->
                <button @click="handleMobileSearch"
                    class="sm:hidden text-slate-50 p-2 focus:outline-none focus:ring-2 focus:ring-amber-400 rounded"
                    aria-label="Search">
                    <i class="fa-solid fa-magnifying-glass"></i>
                </button>

                <!-- Cart -->
                <div @click="emit('openCart')"
                    class="ml-2 relative text-slate-300 cursor-pointer hover:text-white transition">
                    <span
                        class="absolute -top-1 -right-3 text-[10px] text-white bg-red-500 w-4 h-4 rounded-full text-center"
                        role="status" aria-live="polite">{{ totalItems }}</span>
                    <i class="fa-solid fa-shopping-cart text-xl"></i>
                </div>
            </div>
        </div>

        <!-- Mobile off-canvas drawer -->
        <transition name="fade">
            <div v-if="showMobileMenu" class="sm:hidden fixed inset-0 z-50 flex">
                <!-- Overlay -->
                <div class="absolute inset-0 bg-black/50" @click="closeMobileMenu" aria-hidden="true"></div>

                <!-- Drawer panel -->
                <aside ref="mobileDrawer" tabindex="-1" role="dialog" aria-modal="true"
                    aria-labelledby="mobile-nav-title"
                    class="relative z-10 w-3/4 max-w-xs bg-slate-900 text-slate-50 h-full transform transition-transform duration-300 ease-in-out">
                    <div class="flex items-center justify-between p-4 border-b border-slate-800">
                        <h3 id="mobile-nav-title" class="font-semibold">Menu</h3>
                        <button @click="closeMobileMenu" aria-label="Close menu"
                            class="p-2 focus:outline-none focus:ring-2 focus:ring-amber-400 rounded">
                            <i class="fa-solid fa-times"></i>
                        </button>
                    </div>

                    <nav class="p-4 overflow-y-auto h-full">
                        <ul class="flex flex-col space-y-1">
                            <li v-if="props.loading">
                                <span class="p-3 text-slate-400">Loading...</span>
                            </li>
                            <li v-else v-for="cat in props.categories" :key="cat.id">
                                <RouterLink class="block p-3 border-b border-slate-800"
                                    :to="{ name: 'categories', params: { id: cat.id } }"
                                    :class="{ 'bg-green-900': isActiveCategory(cat.id) }" @click="closeMobileMenu">
                                    {{ cat.name }}
                                </RouterLink>
                            </li>
                        </ul>
                    </nav>
                </aside>
            </div>
        </transition>
    </div>
</template>
