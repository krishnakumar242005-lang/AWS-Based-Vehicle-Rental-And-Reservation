
<script setup lang="ts">
import { computed } from 'vue';
import { Link, usePage } from '@inertiajs/vue3';
import { login, register } from '@/routes';
import { index as adminCarsIndex } from '@/routes/admin/cars/index';
import { index as clientReservationsIndex } from '@/routes/client/reservations/index';
import { home, fleet, about, contact } from '@/routes';

const page = usePage();

const role = computed(() => page.props.auth?.user?.role);

const dashboardLink = computed(() =>
    role.value === 'admin'
        ? adminCarsIndex()
        : clientReservationsIndex()
);

const isActive = (path: string) => {
    if (path === '/') return page.url === '/';
    return page.url === path || page.url.startsWith(path + '/');
};
</script>

<template>
    <div class="min-h-screen bg-[#F4F6FA] text-[#101D35]">
        <!-- Navigation -->
        <header class="sticky top-0 z-50 border-b border-[#DCE3ED] bg-white/95 shadow-sm backdrop-blur-xl">
            <div class="mx-auto max-w-7xl px-4 sm:px-6 lg:px-8">
                <nav class="flex min-h-[76px] items-center justify-between gap-4">

                    <!-- Logo -->
                    <Link :href="home()" class="flex shrink-0 items-center gap-3">
                        <div class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#101D35] shadow-lg shadow-blue-950/15">
                            <svg class="h-7 w-7 text-[#C5D0DE]" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M3 13l2-5a2 2 0 012-1.4h10A2 2 0 0119 8l2 5v5h-2v-2H5v2H3v-5z"/>
                                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13h14M7 10h.01M17 10h.01"/>
                            </svg>
                        </div>
                        <div>
                            <p class="text-lg font-black leading-tight tracking-tight text-[#101D35]">
                                REAL<span class="text-[#657F9E]">RENT</span>
                            </p>
                            <p class="mt-1 text-[9px] font-bold tracking-[0.28em] text-slate-500">
                                PREMIUM MOBILITY
                            </p>
                        </div>
                    </Link>

                    <!-- Desktop Navigation -->
                    <div class="hidden items-center gap-1 lg:flex">
                        <Link
                            :href="home()"
                            :class="isActive('/') ? 'bg-[#101D35] text-white' : 'text-slate-600 hover:bg-slate-100 hover:text-[#101D35]'"
                            class="rounded-xl px-4 py-2.5 text-sm font-semibold transition-all"
                        >
                            Home
                        </Link>

                        <Link
                            :href="fleet()"
                            :class="isActive('/fleet') ? 'bg-[#101D35] text-white' : 'text-slate-600 hover:bg-slate-100 hover:text-[#101D35]'"
                            class="rounded-xl px-4 py-2.5 text-sm font-semibold transition-all"
                        >
                            Our Fleet
                        </Link>

                        <Link
                            :href="about()"
                            :class="isActive('/about') ? 'bg-[#101D35] text-white' : 'text-slate-600 hover:bg-slate-100 hover:text-[#101D35]'"
                            class="rounded-xl px-4 py-2.5 text-sm font-semibold transition-all"
                        >
                            About Us
                        </Link>

                        <Link
                            :href="contact()"
                            :class="isActive('/contact') ? 'bg-[#101D35] text-white' : 'text-slate-600 hover:bg-slate-100 hover:text-[#101D35]'"
                            class="rounded-xl px-4 py-2.5 text-sm font-semibold transition-all"
                        >
                            Contact
                        </Link>
                    </div>

                    <!-- Authentication -->
                    <div class="flex shrink-0 items-center gap-2 sm:gap-3">
                        <Link
                            v-if="page.props.auth?.user"
                            :href="dashboardLink"
                            class="inline-flex items-center gap-2 rounded-xl border border-[#DCE3ED] bg-[#F4F6FA] px-3 py-2.5 text-sm font-bold text-[#101D35] transition hover:border-[#101D35] hover:bg-[#E9EEF5] sm:px-5"
                        >
                            <svg class="h-4 w-4" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <rect x="3" y="3" width="7" height="7" rx="1.5"/>
                                <rect x="14" y="3" width="7" height="7" rx="1.5"/>
                                <rect x="3" y="14" width="7" height="7" rx="1.5"/>
                                <rect x="14" y="14" width="7" height="7" rx="1.5"/>
                            </svg>
                            <span class="hidden sm:inline">Dashboard</span>
                        </Link>

                        <template v-else>
                            <Link
                                :href="login()"
                                class="rounded-xl px-2 py-2.5 text-sm font-bold text-[#101D35] transition hover:bg-slate-100 sm:px-4"
                            >
                                Sign In
                            </Link>

                            <Link
                                :href="register()"
                                class="rounded-xl bg-[#101D35] px-3 py-2.5 text-sm font-bold text-white shadow-md shadow-blue-950/15 transition hover:bg-[#263F60] sm:px-5"
                            >
                                <span class="hidden sm:inline">Get Started</span>
                                <span class="sm:hidden">Join</span>
                            </Link>
                        </template>
                    </div>
                </nav>

                <!-- Mobile Navigation -->
                <div class="flex gap-2 overflow-x-auto pb-3 lg:hidden">
                    <Link :href="home()" class="shrink-0 rounded-lg px-3 py-2 text-xs font-semibold" :class="isActive('/') ? 'bg-[#101D35] text-white' : 'bg-slate-100 text-slate-700'">
                        Home
                    </Link>
                    <Link :href="fleet()" class="shrink-0 rounded-lg px-3 py-2 text-xs font-semibold" :class="isActive('/fleet') ? 'bg-[#101D35] text-white' : 'bg-slate-100 text-slate-700'">
                        Our Fleet
                    </Link>
                    <Link :href="about()" class="shrink-0 rounded-lg px-3 py-2 text-xs font-semibold" :class="isActive('/about') ? 'bg-[#101D35] text-white' : 'bg-slate-100 text-slate-700'">
                        About
                    </Link>
                    <Link :href="contact()" class="shrink-0 rounded-lg px-3 py-2 text-xs font-semibold" :class="isActive('/contact') ? 'bg-[#101D35] text-white' : 'bg-slate-100 text-slate-700'">
                        Contact
                    </Link>
                </div>
            </div>
        </header>

        <!-- Current Page -->
        <main class="min-h-[60vh]">
            <slot />
        </main>

        <!-- Footer -->
        <footer class="bg-[#101D35] text-white">
            <div class="mx-auto max-w-7xl px-4 py-14 sm:px-6 lg:px-8">

                <div class="grid gap-10 sm:grid-cols-2 lg:grid-cols-4">

                    <!-- Brand -->
                    <div>
                        <Link :href="home()" class="inline-flex items-center gap-3">
                            <div class="flex h-11 w-11 items-center justify-center rounded-xl border border-white/15 bg-white/10">
                                <svg class="h-7 w-7 text-[#C5D0DE]" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M3 13l2-5a2 2 0 012-1.4h10A2 2 0 0119 8l2 5v5h-2v-2H5v2H3v-5z"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M5 13h14"/>
                                </svg>
                            </div>
                            <div>
                                <h2 class="text-xl font-black tracking-tight">
                                    REAL<span class="text-[#A9BDD5]">RENT</span>
                                </h2>
                                <p class="text-[9px] font-bold tracking-[0.28em] text-slate-400">PREMIUM MOBILITY</p>
                            </div>
                        </Link>

                        <p class="mt-5 max-w-xs text-sm leading-7 text-slate-400">
                            Discover the freedom of the road with a reliable fleet,
                            transparent pricing, and a smoother rental experience.
                        </p>
                    </div>

                    <!-- Quick Links -->
                    <div>
                        <h3 class="text-sm font-bold uppercase tracking-[0.18em] text-[#C5D0DE]">
                            Explore
                        </h3>
                        <ul class="mt-5 space-y-3 text-sm text-slate-400">
                            <li><Link :href="home()" class="transition hover:text-white">Home</Link></li>
                            <li><Link :href="fleet()" class="transition hover:text-white">Browse Our Fleet</Link></li>
                            <li><Link :href="about()" class="transition hover:text-white">About Us</Link></li>
                            <li><Link :href="contact()" class="transition hover:text-white">Contact Us</Link></li>
                        </ul>
                    </div>

                    <!-- Services -->
                    <div>
                        <h3 class="text-sm font-bold uppercase tracking-[0.18em] text-[#C5D0DE]">
                            Our Services
                        </h3>
                        <ul class="mt-5 space-y-3 text-sm text-slate-400">
                            <li>Daily Car Rentals</li>
                            <li>Premium Vehicles</li>
                            <li>Flexible Reservations</li>
                            <li>Customer Support</li>
                        </ul>
                    </div>

                    <!-- Contact -->
                    <div>
                        <h3 class="text-sm font-bold uppercase tracking-[0.18em] text-[#C5D0DE]">
                            Get in Touch
                        </h3>
                        <div class="mt-5 space-y-4 text-sm text-slate-400">
                            <p class="flex items-start gap-3">
                                <svg class="mt-0.5 h-5 w-5 shrink-0 text-[#A9BDD5]" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M3 5a2 2 0 012-2h3.3a1 1 0 011.9 1.3l1.2 3.7a2 2 0 01-1 2.3l-2.2 1.1a11 11 0 005.5 5.5l1.1-2.2a2 2 0 012.3-1l3.7 1.2a2 2 0 011.3 1.9V19a2 2 0 01-2 2h-1C9.7 21 3 14.3 3 6V5z"/>
                                </svg>
                                <span>Contact our support team</span>
                            </p>
                            <p class="flex items-start gap-3">
                                <svg class="mt-0.5 h-5 w-5 shrink-0 text-[#A9BDD5]" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7">
                                    <rect x="3" y="5" width="18" height="14" rx="2"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M3 7l9 6 9-6"/>
                                </svg>
                                <span>Send us your questions through our contact page.</span>
                            </p>
                            <Link :href="contact()" class="inline-flex items-center gap-2 rounded-lg border border-white/15 px-4 py-2.5 font-semibold text-white transition hover:bg-white/10">
                                Contact Us
                                <span aria-hidden="true">→</span>
                            </Link>
                        </div>
                    </div>
                </div>

                <!-- Copyright -->
                <div class="mt-12 flex flex-col gap-3 border-t border-white/10 pt-6 text-center text-xs text-slate-400 sm:flex-row sm:items-center sm:justify-between sm:text-left">
                    <p>© {{ new Date().getFullYear() }} RealRent. All rights reserved.</p>
                    <p>Designed for a smoother journey.</p>
                </div>
            </div>
        </footer>
    </div>
</template>
