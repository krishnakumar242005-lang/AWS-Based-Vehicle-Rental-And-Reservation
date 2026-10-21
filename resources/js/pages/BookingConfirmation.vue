
<script setup lang="ts">
import HomeLayout from '@/layouts/HomeLayout.vue';
import { usePage } from '@inertiajs/vue3';
import { fleet } from '@/routes';
import { index } from '@/routes/client/reservations';
import { computed } from 'vue';

interface Reservation {
    id: number;
    reservation_number: string;
    start_date: string;
    end_date: string;
    pickup_location: string;
    return_location: string;
    driver_license: string;
    phone: string;
    additional_notes?: string;
    total_amount: string;
    status: string;
    created_at: string;
    car: {
        make: string;
        model: string;
        year: number;
        image_url: string;
        description: string;
        fuel_type: string;
    };
    user: {
        name: string;
        email: string;
    };
}

interface PageProps {
    reservation: Reservation;
}

const page = usePage<PageProps>();
const reservation = computed(() => page.props.reservation);

const formatDate = (date: string) => {
    const parsed = new Date(`${date.slice(0, 10)}T00:00:00`);

    if (Number.isNaN(parsed.getTime())) return date;

    return new Intl.DateTimeFormat('en-IN', {
        day: 'numeric',
        month: 'short',
        year: 'numeric',
    }).format(parsed);
};

const formatPrice = (amount: string | number) =>
    new Intl.NumberFormat('en-IN', {
        style: 'currency',
        currency: 'INR',
        maximumFractionDigits: 2,
    }).format(Number(amount) || 0);

const statusClass = computed(() => {
    const status = reservation.value.status.toLowerCase();

    if (status === 'confirmed' || status === 'approved') {
        return 'bg-emerald-100 text-emerald-800';
    }

    if (status === 'cancelled' || status === 'rejected') {
        return 'bg-red-100 text-red-800';
    }

    return 'bg-amber-100 text-amber-800';
});
</script>

<template>
    <HomeLayout>
        <div class="min-h-screen bg-[#F4F6FA] pb-16">
            <!-- Confirmation banner -->
            <section class="relative overflow-hidden bg-[#101D35] px-4 py-12 text-white sm:py-16">
                <div class="pointer-events-none absolute -right-20 -top-24 h-72 w-72 rounded-full border border-white/10"></div>
                <div class="pointer-events-none absolute -right-10 -top-14 h-52 w-52 rounded-full border border-white/10"></div>

                <div class="relative mx-auto max-w-5xl text-center">
                    <div class="mx-auto mb-5 flex h-20 w-20 items-center justify-center rounded-full border border-white/20 bg-white/10">
                        <svg class="h-10 w-10 text-[#C5D0DE]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M5 13l4 4L19 7" />
                        </svg>
                    </div>

                    <p class="mb-3 text-xs font-semibold uppercase tracking-[0.25em] text-[#C5D0DE]">
                        Reservation details
                    </p>

                    <h1 class="text-3xl font-bold sm:text-4xl">
                        Booking submitted successfully
                    </h1>

                    <p class="mx-auto mt-4 max-w-2xl leading-7 text-slate-300">
                        Thank you, {{ reservation.user.name }}. Your reservation details are ready below.
                    </p>

                    <div class="mx-auto mt-7 inline-flex flex-wrap items-center justify-center gap-2 rounded-xl border border-white/15 bg-white/10 px-5 py-3">
                        <span class="text-sm text-slate-300">Reservation number</span>
                        <span class="font-bold tracking-wide text-white">
                            {{ reservation.reservation_number }}
                        </span>
                    </div>
                </div>
            </section>

            <div class="mx-auto max-w-7xl px-4 pt-8 sm:px-6 lg:px-8">
                <div class="grid items-start gap-7 lg:grid-cols-3">
                    <!-- Main details -->
                    <div class="space-y-6 lg:col-span-2">
                        <!-- Vehicle card -->
                        <section class="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm">
                            <div class="flex items-center justify-between border-b border-slate-100 px-5 py-5 sm:px-7">
                                <div>
                                    <p class="text-xs font-semibold uppercase tracking-[0.15em] text-[#58779D]">
                                        Your selected vehicle
                                    </p>
                                    <h2 class="mt-1 text-xl font-bold text-[#101D35]">
                                        Vehicle details
                                    </h2>
                                </div>
                                <span class="rounded-lg bg-slate-100 px-3 py-2 text-xs font-semibold text-slate-600">
                                    {{ reservation.car.year }}
                                </span>
                            </div>

                            <div class="grid sm:grid-cols-5">
                                <div class="h-56 bg-slate-100 sm:col-span-2 sm:h-full">
                                    <img
                                        v-if="reservation.car.image_url"
                                        :src="reservation.car.image_url"
                                        :alt="`${reservation.car.make} ${reservation.car.model}`"
                                        class="h-full min-h-56 w-full object-cover"
                                    />
                                    <div v-else class="flex h-full min-h-56 items-center justify-center text-sm text-slate-500">
                                        Vehicle image unavailable
                                    </div>
                                </div>

                                <div class="p-5 sm:col-span-3 sm:p-7">
                                    <h3 class="text-2xl font-bold text-[#101D35]">
                                        {{ reservation.car.make }} {{ reservation.car.model }}
                                    </h3>

                                    <span class="mt-3 inline-flex rounded-lg bg-slate-100 px-3 py-2 text-sm capitalize text-slate-700">
                                        {{ reservation.car.fuel_type }}
                                    </span>

                                    <p class="mt-5 text-sm leading-7 text-slate-600">
                                        {{ reservation.car.description }}
                                    </p>
                                </div>
                            </div>
                        </section>

                        <!-- Rental information -->
                        <section class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm sm:p-7">
                            <div class="mb-6">
                                <p class="text-xs font-semibold uppercase tracking-[0.15em] text-[#58779D]">
                                    Trip information
                                </p>
                                <h2 class="mt-1 text-xl font-bold text-[#101D35]">
                                    Rental details
                                </h2>
                            </div>

                            <div class="grid gap-6 md:grid-cols-2">
                                <div class="rounded-xl bg-[#F4F6FA] p-5">
                                    <div class="mb-4 flex items-center gap-3">
                                        <div class="flex h-10 w-10 items-center justify-center rounded-lg bg-[#101D35] text-white">
                                            <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M8 7V3m8 4V3m-9 8h10M5 21h14a2 2 0 002-2V7a2 2 0 00-2-2H5a2 2 0 00-2 2v12a2 2 0 002 2z" />
                                            </svg>
                                        </div>
                                        <h3 class="font-bold text-[#101D35]">Rental dates</h3>
                                    </div>

                                    <div class="space-y-4">
                                        <div>
                                            <p class="text-xs text-slate-500">Pickup date</p>
                                            <p class="mt-1 font-semibold text-slate-800">
                                                {{ formatDate(reservation.start_date) }}
                                            </p>
                                        </div>
                                        <div class="border-t border-slate-200 pt-4">
                                            <p class="text-xs text-slate-500">Return date</p>
                                            <p class="mt-1 font-semibold text-slate-800">
                                                {{ formatDate(reservation.end_date) }}
                                            </p>
                                        </div>
                                    </div>
                                </div>

                                <div class="rounded-xl bg-[#F4F6FA] p-5">
                                    <div class="mb-4 flex items-center gap-3">
                                        <div class="flex h-10 w-10 items-center justify-center rounded-lg bg-[#101D35] text-white">
                                            <svg class="h-5 w-5" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M17.657 16.657L13.414 20.9a1.998 1.998 0 01-2.827 0l-4.244-4.243a8 8 0 1111.314 0z" />
                                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M15 11a3 3 0 11-6 0 3 3 0 016 0z" />
                                            </svg>
                                        </div>
                                        <h3 class="font-bold text-[#101D35]">Locations</h3>
                                    </div>

                                    <div class="space-y-4">
                                        <div>
                                            <p class="text-xs text-slate-500">Pickup location</p>
                                            <p class="mt-1 font-semibold text-slate-800">
                                                {{ reservation.pickup_location }}
                                            </p>
                                        </div>
                                        <div class="border-t border-slate-200 pt-4">
                                            <p class="text-xs text-slate-500">Return location</p>
                                            <p class="mt-1 font-semibold text-slate-800">
                                                {{ reservation.return_location }}
                                            </p>
                                        </div>
                                    </div>
                                </div>
                            </div>
                        </section>

                        <!-- Customer details -->
                        <section class="rounded-2xl border border-slate-200 bg-white p-5 shadow-sm sm:p-7">
                            <p class="text-xs font-semibold uppercase tracking-[0.15em] text-[#58779D]">
                                Reservation owner
                            </p>
                            <h2 class="mb-6 mt-1 text-xl font-bold text-[#101D35]">
                                Contact information
                            </h2>

                            <div class="grid gap-5 sm:grid-cols-2">
                                <div class="rounded-xl border border-slate-200 p-4">
                                    <p class="text-sm text-slate-500">Full name</p>
                                    <p class="mt-1 break-words font-semibold text-[#101D35]">
                                        {{ reservation.user.name }}
                                    </p>
                                </div>

                                <div class="rounded-xl border border-slate-200 p-4">
                                    <p class="text-sm text-slate-500">Email address</p>
                                    <p class="mt-1 break-all font-semibold text-[#101D35]">
                                        {{ reservation.user.email }}
                                    </p>
                                </div>
                            </div>
                        </section>
                    </div>

                    <!-- Sidebar -->
                    <aside class="space-y-6 lg:sticky lg:top-6">
                        <section class="overflow-hidden rounded-2xl border border-slate-200 bg-white shadow-sm">
                            <div class="bg-[#101D35] p-6 text-white">
                                <p class="text-sm text-slate-300">Reservation overview</p>
                                <h2 class="mt-1 text-2xl font-bold">Booking summary</h2>
                            </div>

                            <div class="space-y-5 p-6">
                                <div>
                                    <p class="mb-2 text-sm text-slate-500">Current status</p>
                                    <span class="inline-flex rounded-full px-3 py-1.5 text-sm font-semibold capitalize" :class="statusClass">
                                        {{ reservation.status }}
                                    </span>
                                </div>

                                <div class="border-t border-slate-200 pt-5">
                                    <p class="text-sm text-slate-500">Reservation number</p>
                                    <p class="mt-1 break-words font-bold text-[#101D35]">
                                        {{ reservation.reservation_number }}
                                    </p>
                                </div>

                                <div class="border-t border-slate-200 pt-5">
                                    <p class="text-sm text-slate-500">Total reservation amount</p>
                                    <p class="mt-2 text-3xl font-bold text-[#101D35]">
                                        {{ formatPrice(reservation.total_amount) }}
                                    </p>
                                </div>

                                <div class="rounded-xl border border-slate-200 bg-[#F4F6FA] p-4">
                                    <p class="text-sm font-semibold text-[#101D35]">
                                        Keep your reservation number
                                    </p>
                                    <p class="mt-1 text-sm leading-6 text-slate-600">
                                        Use it to identify your booking when checking its status.
                                    </p>
                                </div>
                            </div>
                        </section>

                        <section class="rounded-2xl border border-slate-200 bg-white p-6 shadow-sm">
                            <h2 class="text-lg font-bold text-[#101D35]">
                                What's next?
                            </h2>

                            <div class="mt-5 space-y-5">
                                <div class="flex gap-3">
                                    <span class="flex h-8 w-8 shrink-0 items-center justify-center rounded-full bg-[#101D35] text-sm font-bold text-white">1</span>
                                    <div>
                                        <p class="font-semibold text-slate-800">Check booking status</p>
                                        <p class="mt-1 text-sm leading-6 text-slate-500">
                                            Review the current status in your reservations.
                                        </p>
                                    </div>
                                </div>

                                <div class="flex gap-3">
                                    <span class="flex h-8 w-8 shrink-0 items-center justify-center rounded-full bg-[#101D35] text-sm font-bold text-white">2</span>
                                    <div>
                                        <p class="font-semibold text-slate-800">Watch for updates</p>
                                        <p class="mt-1 text-sm leading-6 text-slate-500">
                                            Check for any booking updates or instructions.
                                        </p>
                                    </div>
                                </div>

                                <div class="flex gap-3">
                                    <span class="flex h-8 w-8 shrink-0 items-center justify-center rounded-full bg-[#101D35] text-sm font-bold text-white">3</span>
                                    <div>
                                        <p class="font-semibold text-slate-800">Prepare your documents</p>
                                        <p class="mt-1 text-sm leading-6 text-slate-500">
                                            Keep your driving licence ready for pickup.
                                        </p>
                                    </div>
                                </div>
                            </div>
                        </section>

                        <div class="space-y-3">
                            <a
                                :href="index.url()"
                                class="flex w-full items-center justify-center rounded-xl bg-[#101D35] px-5 py-4 text-center font-semibold text-white transition hover:bg-[#263F60]"
                            >
                                View my bookings
                            </a>

                            <a
                                :href="fleet.url()"
                                class="flex w-full items-center justify-center rounded-xl border border-slate-300 bg-white px-5 py-4 text-center font-semibold text-[#101D35] transition hover:bg-slate-100"
                            >
                                Browse more cars
                            </a>
                        </div>
                    </aside>
                </div>
            </div>
        </div>
    </HomeLayout>
</template>
