<script setup lang="ts">
import { ref } from 'vue'

const email = ref('')
const message = ref('')

const subscribe = () => {
    if (!email.value || !email.value.includes('@')) {
        message.value = 'Please enter a valid email.'
        return
    }

    // Simulate subscribe action
    message.value = 'Thanks for subscribing!'
    setTimeout(() => {
        email.value = ''
        message.value = ''
    }, 2500)
}
</script>

<template>
    <footer class="bg-slate-900 text-slate-200 mt-5">
        <div class="container mx-auto py-10 px-4">
            <div class="flex flex-col md:flex-row md:justify-between gap-8">
                <div class="max-w-sm">
                    <h3 class="text-xl md:text-2xl font-bold">AllWays Store</h3>
                    <p class="mt-3 text-slate-300 text-sm">Modern apparel for everyday style. Quality fabrics, fair
                        prices, and fast shipping.</p>
                </div>

                <div class="grid grid-cols-2 sm:grid-cols-3 gap-6">
                    <div>
                        <h4 class="font-semibold mb-2">Shop</h4>
                        <ul class="text-sm text-slate-300 space-y-2">
                            <li>
                                <RouterLink to="/" class="hover:text-white">All Products</RouterLink>
                            </li>
                            <li>
                                <RouterLink to="/categories/1" class="hover:text-white">New Arrivals</RouterLink>
                            </li>
                            <li>
                                <RouterLink to="/categories/2" class="hover:text-white">Best Sellers</RouterLink>
                            </li>
                        </ul>
                    </div>

                    <div>
                        <h4 class="font-semibold mb-2">Company</h4>
                        <ul class="text-sm text-slate-300 space-y-2">
                            <li><a class="hover:text-white">About Us</a></li>
                            <li><a class="hover:text-white">Careers</a></li>
                            <li><a class="hover:text-white">Press</a></li>
                        </ul>
                    </div>

                    <div>
                        <h4 class="font-semibold mb-2">Support</h4>
                        <ul class="text-sm text-slate-300 space-y-2">
                            <li><a class="hover:text-white">Help Center</a></li>
                            <li><a class="hover:text-white">Returns</a></li>
                            <li><a class="hover:text-white">Shipping</a></li>
                        </ul>
                    </div>
                </div>

                <div class="max-w-xs">
                    <h4 class="font-semibold mb-2">Join our newsletter</h4>
                    <p class="text-sm text-slate-300 mb-3">Get 10% off your first order and updates about new
                        collections.</p>
                    <div class="flex gap-2">
                        <input v-model="email" type="email" placeholder="Your email"
                            class="w-full px-3 py-2 rounded bg-slate-800 text-slate-100 outline-none" />
                        <button @click="subscribe"
                            class="bg-amber-500 text-slate-900 px-4 py-2 rounded font-semibold hover:opacity-90">Subscribe</button>
                    </div>
                    <p v-if="message" class="mt-2 text-xs text-emerald-300">{{ message }}</p>
                </div>
            </div>

            <div
                class="mt-8 border-t border-slate-700 pt-4 flex flex-col md:flex-row md:justify-between items-center gap-4">
                <div class="text-sm text-slate-400">© 2025 AllWays Store. All rights reserved.</div>
                <div class="flex items-center gap-4 text-slate-300">
                    <a class="hover:text-white"><i class="fa-brands fa-facebook-f"></i></a>
                    <a class="hover:text-white"><i class="fa-brands fa-instagram"></i></a>
                    <a class="hover:text-white"><i class="fa-brands fa-twitter"></i></a>
                    <a class="hover:text-white"><i class="fa-brands fa-pinterest"></i></a>
                </div>
            </div>
        </div>
    </footer>
</template>
