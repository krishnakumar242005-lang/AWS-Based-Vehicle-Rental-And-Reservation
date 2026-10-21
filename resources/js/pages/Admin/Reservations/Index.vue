
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link, router } from '@inertiajs/vue3';
import { computed, ref, watch } from 'vue';
import { show, index } from '@/routes/admin/reservations';

const props = defineProps<{
    reservations: {
        data: Array<{
            id: number;
            reservation_number: string;
            user: { id: number; name: string; email: string } | null;
            car: {
                id: number;
                make: string;
                model: string;
                year: number;
                license_plate: string;
            } | null;
            start_date: string;
            end_date: string;
            total_days: number;
            total_amount: number | string;
            status: string;
        }>;
        links: Array<{ url: string | null; label: string; active: boolean }>;
    };
    filters: { search?: string; status?: string };
    statuses: Record<string, { label: string; count: number; color: string }>;
    currency: { symbol: string; code: string };
}>();

const search = ref(props.filters?.search || '');
const statusFilter = ref(props.filters?.status || 'all');

const totalReservations = computed(() =>
    Object.values(props.statuses || {}).reduce(
        (sum, status) => sum + status.count,
        0,
    ),
);

function doSearch() {
    router.get(
        index().url,
        {
            search: search.value || null,
            status: statusFilter.value === 'all' ? null : statusFilter.value,
        },
        {
            preserveState: true,
            preserveScroll: true,
            replace: true,
        },
    );
}

watch(search, (value, oldValue) => {
    if (value === '' && oldValue !== '') doSearch();
});

function navigateToReservation(id: number) {
    router.visit(show(id).url);
}

function formatDate(date: string) {
    if (!date) return '—';

    const parsed = new Date(date.slice(0, 10) + 'T00:00:00');

    return Number.isNaN(parsed.getTime())
        ? date
        : new Intl.DateTimeFormat('en-IN', {
              day: '2-digit',
              month: 'short',
              year: 'numeric',
          }).format(parsed);
}

function formatAmount(amount: number | string) {
    const value = Number(amount);

    return Number.isFinite(value)
        ? value.toLocaleString('en-IN', {
              minimumFractionDigits: 2,
              maximumFractionDigits: 2,
          })
        : '0.00';
}

function statusStyle(status: string) {
    const color = props.statuses?.[status]?.color || '#64748B';

    return {
        backgroundColor: `${color}18`,
        color,
        borderColor: `${color}50`,
    };
}
</script>

<template>
    <Head title="Admin Reservations" />

    <AdminLayout>
        <main class="min-h-screen flex-1 space-y-6 bg-[#F4F6FA] p-4 sm:p-6 lg:p-8">
            <!-- Page heading -->
            <section class="relative overflow-hidden rounded-2xl bg-gradient-to-br from-[#101D35] via-[#1B3150] to-[#263F60] p-6 text-white shadow-lg sm:p-8">
                <div class="relative z-10">
                    <p class="text-xs font-semibold uppercase tracking-[0.22em] text-[#C5D0DE]">
                        Management / Bookings
                    </p>
                    <h1 class="mt-3 text-3xl font-bold tracking-tight sm:text-4xl">
                        Reservations
                    </h1>
                    <p class="mt-3 max-w-xl text-sm leading-6 text-slate-300 sm:text-base">
                        Manage customer bookings, rental periods, reservation
                        statuses, and booking amounts from one place.
                    </p>

                    <div class="mt-6 inline-flex items-center gap-3 rounded-xl border border-white/15 bg-white/10 px-4 py-3">
                        <span class="flex h-10 w-10 items-center justify-center rounded-lg bg-white/10 text-[#C5D0DE]">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.7">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M8 7V3m8 4V3M4 11h16M5 5h14a1 1 0 011 1v13a1 1 0 01-1 1H5a1 1 0 01-1-1V6a1 1 0 011-1z" />
                            </svg>
                        </span>
                        <div>
                            <p class="text-xs text-slate-300">Total reservations</p>
                            <p class="text-xl font-bold">{{ totalReservations }}</p>
                        </div>
                    </div>
                </div>

                <div class="pointer-events-none absolute -right-12 -top-20 h-64 w-64 rounded-full border border-white/10"></div>
                <div class="pointer-events-none absolute -right-2 -top-12 h-48 w-48 rounded-full border border-white/10"></div>
            </section>

            <!-- Search and filters -->
            <section class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm sm:p-6">
                <div class="flex flex-col gap-4 lg:flex-row lg:items-end lg:justify-between">
                    <div class="flex-1">
                        <h2 class="text-lg font-bold text-[#101D35]">Find a reservation</h2>
                        <p class="mt-1 text-sm text-slate-500">
                            Search by booking number, client, vehicle, or registration plate.
                        </p>

                        <div class="mt-4 flex flex-col gap-3 sm:flex-row">
                            <div class="relative w-full max-w-xl">
                                <svg xmlns="http://www.w3.org/2000/svg" class="pointer-events-none absolute left-3 top-1/2 h-5 w-5 -translate-y-1/2 text-[#58779D]" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.7">
                                    <circle cx="11" cy="11" r="7" />
                                    <path stroke-linecap="round" d="m16 16 4 4" />
                                </svg>
                                <input
                                    v-model="search"
                                    type="search"
                                    placeholder="Search reservations..."
                                    class="w-full rounded-xl border border-[#DCE3ED] bg-[#F8FAFD] py-3 pl-10 pr-4 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-2 focus:ring-[#58779D]/15"
                                    @keyup.enter="doSearch"
                                />
                            </div>
                            <button
                                type="button"
                                class="rounded-xl bg-[#101D35] px-5 py-3 text-sm font-semibold text-white transition hover:bg-[#263F60]"
                                @click="doSearch"
                            >
                                Search
                            </button>
                        </div>
                    </div>
                </div>

                <div class="mt-5 border-t border-[#E8EDF4] pt-5">
                    <p class="mb-3 text-xs font-semibold uppercase tracking-wider text-slate-500">
                        Filter by status
                    </p>

                    <div class="flex flex-wrap gap-2">
                        <button
                            type="button"
                            class="rounded-full border px-4 py-2 text-sm font-semibold transition"
                            :class="statusFilter === 'all'
                                ? 'border-[#101D35] bg-[#101D35] text-white'
                                : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                            @click="statusFilter = 'all'; doSearch()"
                        >
                            All <span class="ml-1 opacity-75">({{ totalReservations }})</span>
                        </button>

                        <button
                            v-for="(status, key) in props.statuses"
                            :key="key"
                            type="button"
                            class="inline-flex items-center gap-2 rounded-full border px-4 py-2 text-sm font-semibold transition"
                            :style="statusFilter === key
                                ? { backgroundColor: status.color, color: '#FFFFFF', borderColor: status.color }
                                : { backgroundColor: '#FFFFFF', color: '#263F60', borderColor: '#DCE3ED' }"
                            @click="statusFilter = key; doSearch()"
                        >
                            <span
                                class="h-2 w-2 rounded-full"
                                :style="{ backgroundColor: statusFilter === key ? '#FFFFFF' : status.color }"
                            ></span>
                            {{ status.label }}
                            <span class="opacity-75">({{ status.count }})</span>
                        </button>
                    </div>
                </div>
            </section>

            <!-- Reservation table -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex flex-col gap-2 border-b border-[#E5EAF1] px-5 py-5 sm:flex-row sm:items-center sm:justify-between sm:px-6">
                    <div>
                        <h2 class="text-lg font-bold text-[#101D35]">All bookings</h2>
                        <p class="mt-1 text-sm text-slate-500">
                            Select a booking to inspect its full details.
                        </p>
                    </div>
                    <span class="w-fit rounded-full bg-[#F4F6FA] px-3 py-1.5 text-xs font-semibold text-[#263F60] ring-1 ring-[#DCE3ED]">
                        {{ props.reservations.data.length }} shown
                    </span>
                </div>

                <div v-if="props.reservations.data.length === 0" class="px-6 py-16 text-center">
                    <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-2xl bg-[#F4F6FA] text-[#58779D]">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-8 w-8" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                            <path stroke-linecap="round" stroke-linejoin="round" d="M8 7V3m8 4V3M4 11h16M5 5h14a1 1 0 011 1v13a1 1 0 01-1 1H5a1 1 0 01-1-1V6a1 1 0 011-1z" />
                        </svg>
                    </div>
                    <h3 class="mt-5 text-lg font-bold text-[#101D35]">No reservations found</h3>
                    <p class="mt-2 text-sm text-slate-500">
                        Try another search term or select a different status.
                    </p>
                    <button
                        type="button"
                        class="mt-5 rounded-xl border border-[#DCE3ED] px-4 py-2.5 text-sm font-semibold text-[#263F60] hover:bg-[#F4F6FA]"
                        @click="search = ''; statusFilter = 'all'; doSearch()"
                    >
                        Clear filters
                    </button>
                </div>

                <!-- Desktop table -->
                <div v-else class="hidden overflow-x-auto md:block">
                    <table class="w-full min-w-[950px] text-left">
                        <thead class="bg-[#F4F6FA]">
                            <tr class="text-xs font-semibold uppercase tracking-wider text-slate-500">
                                <th class="px-5 py-4">Reservation</th>
                                <th class="px-5 py-4">Client</th>
                                <th class="px-5 py-4">Vehicle</th>
                                <th class="px-5 py-4">Rental period</th>
                                <th class="px-5 py-4">Total</th>
                                <th class="px-5 py-4">Status</th>
                                <th class="px-5 py-4 text-right">Details</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-[#E8EDF4]">
                            <tr
                                v-for="res in props.reservations.data"
                                :key="res.id"
                                class="cursor-pointer transition-colors hover:bg-[#F8FAFD]"
                                @click="navigateToReservation(res.id)"
                            >
                                <td class="px-5 py-5">
                                    <p class="font-bold text-[#101D35]">{{ res.reservation_number }}</p>
                                    <p class="mt-1 text-xs text-slate-400">Booking #{{ res.id }}</p>
                                </td>
                                <td class="px-5 py-5">
                                    <p class="font-semibold text-[#263F60]">{{ res.user?.name || 'Unknown client' }}</p>
                                    <p class="mt-1 text-xs text-slate-500">{{ res.user?.email || '—' }}</p>
                                </td>
                                <td class="px-5 py-5">
                                    <p class="font-semibold text-[#263F60]">
                                        {{ res.car ? `${res.car.year} ${res.car.make} ${res.car.model}` : 'Vehicle unavailable' }}
                                    </p>
                                    <p class="mt-1 text-xs text-slate-500">{{ res.car?.license_plate || '—' }}</p>
                                </td>
                                <td class="px-5 py-5">
                                    <p class="text-sm font-medium text-[#101D35]">{{ formatDate(res.start_date) }}</p>
                                    <p class="my-1 text-xs text-slate-400">to</p>
                                    <p class="text-sm font-medium text-[#101D35]">{{ formatDate(res.end_date) }}</p>
                                    <p class="mt-2 text-xs text-slate-500">{{ res.total_days }} rental days</p>
                                </td>
                                <td class="px-5 py-5">
                                    <p class="font-bold text-[#101D35]">
                                        {{ props.currency.symbol }}{{ formatAmount(res.total_amount) }}
                                    </p>
                                    <p class="mt-1 text-xs text-slate-400">{{ props.currency.code }}</p>
                                </td>
                                <td class="px-5 py-5">
                                    <span
                                        class="inline-flex items-center gap-2 rounded-full border px-3 py-1.5 text-xs font-semibold"
                                        :style="statusStyle(res.status)"
                                    >
                                        <span class="h-2 w-2 rounded-full" :style="{ backgroundColor: props.statuses[res.status]?.color || '#64748B' }"></span>
                                        {{ props.statuses[res.status]?.label || res.status }}
                                    </span>
                                </td>
                                <td class="px-5 py-5 text-right">
                                    <button
                                        type="button"
                                        class="rounded-lg border border-[#DCE3ED] px-3 py-2 text-sm font-semibold text-[#263F60] transition hover:border-[#263F60] hover:bg-[#F4F6FA]"
                                        @click.stop="navigateToReservation(res.id)"
                                    >
                                        View →
                                    </button>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Mobile cards -->
                <div v-if="props.reservations.data.length > 0" class="space-y-3 p-4 md:hidden">
                    <button
                        v-for="res in props.reservations.data"
                        :key="res.id"
                        type="button"
                        class="block w-full rounded-xl border border-[#DCE3ED] bg-white p-4 text-left transition hover:border-[#58779D] hover:bg-[#F8FAFD]"
                        @click="navigateToReservation(res.id)"
                    >
                        <div class="flex items-start justify-between gap-3">
                            <div class="min-w-0">
                                <p class="break-words text-sm font-bold text-[#101D35]">{{ res.reservation_number }}</p>
                                <p class="mt-1 text-sm text-[#58779D]">{{ res.user?.name || 'Unknown client' }}</p>
                                <p class="mt-1 break-all text-xs text-slate-500">{{ res.user?.email || '—' }}</p>
                            </div>
                            <span class="shrink-0 rounded-full border px-2.5 py-1 text-xs font-semibold" :style="statusStyle(res.status)">
                                {{ props.statuses[res.status]?.label || res.status }}
                            </span>
                        </div>

                        <div class="my-4 border-y border-[#E8EDF4] py-3">
                            <p class="text-sm font-semibold text-[#263F60]">
                                {{ res.car ? `${res.car.year} ${res.car.make} ${res.car.model}` : 'Vehicle unavailable' }}
                            </p>
                            <p class="mt-1 text-xs text-slate-500">{{ res.car?.license_plate || '—' }}</p>
                        </div>

                        <div class="grid grid-cols-2 gap-4">
                            <div>
                                <p class="text-xs text-slate-500">Pickup</p>
                                <p class="mt-1 text-sm font-medium text-[#101D35]">{{ formatDate(res.start_date) }}</p>
                            </div>
                            <div>
                                <p class="text-xs text-slate-500">Return</p>
                                <p class="mt-1 text-sm font-medium text-[#101D35]">{{ formatDate(res.end_date) }}</p>
                            </div>
                            <div>
                                <p class="text-xs text-slate-500">Duration</p>
                                <p class="mt-1 text-sm font-medium text-[#101D35]">{{ res.total_days }} days</p>
                            </div>
                            <div>
                                <p class="text-xs text-slate-500">Total amount</p>
                                <p class="mt-1 text-sm font-bold text-[#101D35]">{{ props.currency.symbol }}{{ formatAmount(res.total_amount) }}</p>
                            </div>
                        </div>
                        <p class="mt-4 text-sm font-semibold text-[#263F60]">View reservation details →</p>
                    </button>
                </div>

                <!-- Pagination -->
                <nav
                    v-if="props.reservations.links?.length && props.reservations.data.length > 0"
                    aria-label="Reservation pages"
                    class="flex flex-wrap items-center gap-2 border-t border-[#E5EAF1] px-4 py-4 sm:px-6"
                >
                    <Link
                        v-for="(link, i) in props.reservations.links"
                        :key="i"
                        :href="link.url || '#'"
                        :preserve-scroll="true"
                        :class="[
                            'rounded-lg border px-3 py-2 text-sm font-medium transition',
                            link.active
                                ? 'border-[#101D35] bg-[#101D35] text-white'
                                : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]',
                            !link.url && 'pointer-events-none opacity-40',
                        ]"
                    >
                        <span v-html="link.label" />
                    </Link>
                </nav>
            </section>
        </main>
    </AdminLayout>
</template>
