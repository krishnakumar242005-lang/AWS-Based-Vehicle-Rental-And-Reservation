
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link } from '@inertiajs/vue3';
import { computed } from 'vue';
import { index, edit, print } from '@/routes/admin/reservations';

const props = defineProps<{
    reservation: any;
    statusMeta: Array<{ value: string; label: string; color: string }>;
    paymentStatusMeta: Array<{ value: string; label: string }>;
    currency: { symbol: string; code: string };
}>();

const statusMap = computed(() => {
    const map: Record<string, { label: string; color: string }> = {};
    for (const status of props.statusMeta || []) {
        map[status.value] = { label: status.label, color: status.color };
    }
    return map;
});

function getStatusStyle(status: string) {
    const meta = statusMap.value[status];
    if (!meta) {
        return {
            bg: '#F1F5F9',
            text: '#475569',
            dot: '#64748B',
            label: status || 'Unknown',
        };
    }

    return {
        bg: `${meta.color}18`,
        text: meta.color,
        dot: meta.color,
        label: meta.label,
    };
}

function formatDate(date?: string) {
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

function formatDateTime(date?: string) {
    if (!date) return '—';
    const parsed = new Date(date);

    return Number.isNaN(parsed.getTime())
        ? date
        : new Intl.DateTimeFormat('en-IN', {
              dateStyle: 'medium',
              timeStyle: 'short',
          }).format(parsed);
}

function formatMoney(amount?: number | string) {
    const value = Number(amount ?? 0);
    const formatted = Number.isFinite(value)
        ? value.toLocaleString('en-IN', {
              minimumFractionDigits: 2,
              maximumFractionDigits: 2,
          })
        : '0.00';

    return `${props.currency.symbol}${formatted}`;
}
</script>

<template>
    <Head :title="`Reservation ${reservation?.reservation_number || ''}`" />

    <AdminLayout>
        <main class="min-h-screen flex-1 space-y-6 bg-[#F4F6FA] p-4 sm:p-6 lg:p-8">
            <!-- Page heading -->
            <section class="relative overflow-hidden rounded-2xl bg-gradient-to-br from-[#101D35] via-[#1B3150] to-[#263F60] p-6 text-white shadow-lg sm:p-8">
                <div class="relative z-10">
                    <p class="text-xs font-semibold uppercase tracking-[0.22em] text-[#C5D0DE]">
                        Management / Reservations
                    </p>

                    <h1 class="mt-3 break-words text-2xl font-bold tracking-tight sm:text-4xl">
                        Reservation Details
                    </h1>

                    <p class="mt-2 break-words text-sm text-slate-300 sm:text-base">
                        {{ reservation?.reservation_number || 'Reservation record' }}
                    </p>

                    <div class="mt-5 flex flex-wrap gap-3">
                        <span
                            class="inline-flex items-center gap-2 rounded-full px-3 py-1.5 text-xs font-semibold"
                            :style="{
                                backgroundColor: getStatusStyle(reservation.status).bg,
                                color: getStatusStyle(reservation.status).text,
                            }"
                        >
                            <span class="h-2 w-2 rounded-full" :style="{ backgroundColor: getStatusStyle(reservation.status).dot }"></span>
                            {{ getStatusStyle(reservation.status).label }}
                        </span>

                        <span class="rounded-full border border-white/20 bg-white/10 px-3 py-1.5 text-xs font-medium text-slate-200">
                            Booking #{{ reservation.id }}
                        </span>
                    </div>
                </div>

                <div class="pointer-events-none absolute -right-12 -top-20 h-64 w-64 rounded-full border border-white/10"></div>
                <div class="pointer-events-none absolute -right-2 -top-12 h-48 w-48 rounded-full border border-white/10"></div>
            </section>

            <!-- Actions and total -->
            <section class="flex flex-col gap-4 rounded-2xl border border-[#DCE3ED] bg-white p-4 shadow-sm sm:flex-row sm:items-center sm:justify-between sm:p-5">
                <div>
                    <p class="text-sm text-slate-500">Total reservation amount</p>
                    <p class="mt-1 text-2xl font-bold text-[#101D35] sm:text-3xl">
                        {{ formatMoney(reservation.total_amount) }}
                    </p>
                    <p class="mt-1 text-xs text-slate-500">{{ currency.code }}</p>
                </div>

                <div class="flex flex-wrap gap-2">
                    <Link
                        :href="index().url"
                        class="inline-flex items-center justify-center rounded-xl border border-[#DCE3ED] px-4 py-2.5 text-sm font-semibold text-[#263F60] transition hover:bg-[#F4F6FA]"
                    >
                        ← Back
                    </Link>

                    <Link
                        :href="edit(reservation.id).url"
                        class="inline-flex items-center justify-center rounded-xl bg-[#263F60] px-4 py-2.5 text-sm font-semibold text-white transition hover:bg-[#101D35]"
                    >
                        Edit reservation
                    </Link>

                    <a
                        :href="print(reservation.id).url"
                        target="_blank"
                        rel="noopener"
                        class="inline-flex items-center justify-center rounded-xl border border-[#DCE3ED] bg-[#F4F6FA] px-4 py-2.5 text-sm font-semibold text-[#263F60] transition hover:bg-[#E8EDF4]"
                    >
                        Print ↗
                    </a>
                </div>
            </section>

            <!-- Client and vehicle -->
            <section class="grid grid-cols-1 gap-5 lg:grid-cols-2">
                <div class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                    <div class="flex items-center gap-3 border-b border-[#E5EAF1] px-5 py-4">
                        <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#F4F6FA] text-[#58779D]">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.7">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M16 21v-2a4 4 0 00-4-4H8a4 4 0 00-4 4v2m6-10a4 4 0 100-8 4 4 0 000 8zm10 10v-2a4 4 0 00-3-3.87m-1-11.98a4 4 0 010 7.75" />
                            </svg>
                        </div>
                        <div>
                            <h2 class="font-bold text-[#101D35]">Client information</h2>
                            <p class="text-xs text-slate-500">Customer associated with this booking</p>
                        </div>
                    </div>

                    <div class="space-y-5 p-5">
                        <div>
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Full name</p>
                            <p class="mt-1 break-words font-semibold text-[#101D35]">{{ reservation.user?.name || '—' }}</p>
                        </div>
                        <div>
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Email address</p>
                            <p class="mt-1 break-all font-medium text-[#263F60]">{{ reservation.user?.email || '—' }}</p>
                        </div>
                        <div v-if="reservation.user?.id">
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Client ID</p>
                            <p class="mt-1 font-medium text-[#101D35]">#{{ reservation.user.id }}</p>
                        </div>
                    </div>
                </div>

                <div class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                    <div class="flex items-center gap-3 border-b border-[#E5EAF1] px-5 py-4">
                        <div class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#F4F6FA] text-[#58779D]">
                            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.7">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M3 13l2-6h14l2 6m-18 0h18M5 13v5m14-5v5M7 18h10M7 10h.01M17 10h.01M5 7l1-3h12l1 3" />
                            </svg>
                        </div>
                        <div>
                            <h2 class="font-bold text-[#101D35]">Vehicle information</h2>
                            <p class="text-xs text-slate-500">Vehicle assigned to the reservation</p>
                        </div>
                    </div>

                    <div class="space-y-5 p-5">
                        <div>
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Vehicle</p>
                            <p class="mt-1 font-semibold text-[#101D35]">
                                {{ reservation.car ? `${reservation.car.year} ${reservation.car.make} ${reservation.car.model}` : 'Vehicle unavailable' }}
                            </p>
                        </div>
                        <div>
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Registration plate</p>
                            <p class="mt-2 inline-flex rounded-lg border border-[#DCE3ED] bg-[#F4F6FA] px-3 py-1.5 font-semibold tracking-wider text-[#263F60]">
                                {{ reservation.car?.license_plate || '—' }}
                            </p>
                        </div>
                        <div v-if="reservation.car?.id">
                            <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Vehicle ID</p>
                            <p class="mt-1 font-medium text-[#101D35]">#{{ reservation.car.id }}</p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Reservation details -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="border-b border-[#E5EAF1] px-5 py-4 sm:px-6">
                    <h2 class="font-bold text-[#101D35]">Rental details</h2>
                    <p class="mt-1 text-sm text-slate-500">Dates, timings, and pickup information</p>
                </div>

                <div class="grid grid-cols-1 gap-5 p-5 sm:grid-cols-2 lg:grid-cols-3 sm:p-6">
                    <div class="rounded-xl bg-[#F8FAFD] p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Pickup date & time</p>
                        <p class="mt-2 font-semibold text-[#101D35]">{{ formatDate(reservation.start_date) }}</p>
                        <p class="mt-1 text-sm text-[#58779D]">{{ reservation.pickup_time || 'Time not specified' }}</p>
                    </div>

                    <div class="rounded-xl bg-[#F8FAFD] p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Return date & time</p>
                        <p class="mt-2 font-semibold text-[#101D35]">{{ formatDate(reservation.end_date) }}</p>
                        <p class="mt-1 text-sm text-[#58779D]">{{ reservation.return_time || 'Time not specified' }}</p>
                    </div>

                    <div class="rounded-xl bg-[#F8FAFD] p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Rental duration</p>
                        <p class="mt-2 text-2xl font-bold text-[#101D35]">{{ reservation.total_days ?? 0 }}</p>
                        <p class="mt-1 text-sm text-slate-500">Days</p>
                    </div>

                    <div class="rounded-xl border border-[#E5EAF1] p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Pickup location</p>
                        <p class="mt-2 break-words font-semibold text-[#101D35]">{{ reservation.pickup_location || '—' }}</p>
                    </div>

                    <div class="rounded-xl border border-[#E5EAF1] p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-slate-500">Return location</p>
                        <p class="mt-2 break-words font-semibold text-[#101D35]">{{ reservation.return_location || '—' }}</p>
                    </div>

                    <div v-if="reservation.status === 'cancelled'" class="rounded-xl border border-rose-200 bg-rose-50 p-4">
                        <p class="text-xs font-medium uppercase tracking-wider text-rose-700">Cancellation details</p>
                        <p class="mt-2 text-sm font-semibold text-rose-800">{{ formatDateTime(reservation.cancelled_at) }}</p>
                        <p class="mt-3 text-xs font-medium text-rose-700">Reason</p>
                        <p class="mt-1 break-words text-sm text-rose-800">{{ reservation.cancellation_reason || 'No reason provided' }}</p>
                    </div>
                </div>
            </section>

            <!-- Price breakdown -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="border-b border-[#E5EAF1] px-5 py-4 sm:px-6">
                    <h2 class="font-bold text-[#101D35]">Price breakdown</h2>
                    <p class="mt-1 text-sm text-slate-500">Summary of reservation charges</p>
                </div>

                <div class="space-y-4 p-5 sm:p-6">
                    <div class="flex items-center justify-between gap-4">
                        <span class="text-sm text-slate-600">Daily rate</span>
                        <span class="text-right font-semibold text-[#101D35]">{{ formatMoney(reservation.daily_rate) }}</span>
                    </div>
                    <div class="flex items-center justify-between gap-4">
                        <span class="text-sm text-slate-600">Subtotal</span>
                        <span class="text-right font-semibold text-[#101D35]">{{ formatMoney(reservation.subtotal) }}</span>
                    </div>
                    <div class="flex items-center justify-between gap-4">
                        <span class="text-sm text-slate-600">Tax</span>
                        <span class="text-right font-semibold text-[#101D35]">{{ formatMoney(reservation.tax_amount) }}</span>
                    </div>
                    <div class="flex items-center justify-between gap-4">
                        <span class="text-sm text-slate-600">Discount</span>
                        <span class="text-right font-semibold text-emerald-700">−{{ formatMoney(reservation.discount_amount) }}</span>
                    </div>
                    <div class="flex items-center justify-between gap-4 border-t border-[#E5EAF1] pt-4">
                        <div>
                            <p class="font-bold text-[#101D35]">Total amount</p>
                            <p class="mt-1 text-xs text-slate-500">{{ currency.code }}</p>
                        </div>
                        <p class="text-2xl font-bold text-[#101D35]">{{ formatMoney(reservation.total_amount) }}</p>
                    </div>
                </div>
            </section>

            <!-- Payments -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="border-b border-[#E5EAF1] px-5 py-4 sm:px-6">
                    <h2 class="font-bold text-[#101D35]">Payment history</h2>
                    <p class="mt-1 text-sm text-slate-500">Payments recorded for this reservation</p>
                </div>

                <div v-if="!reservation.payments || reservation.payments.length === 0" class="px-6 py-12 text-center">
                    <div class="mx-auto flex h-14 w-14 items-center justify-center rounded-2xl bg-[#F4F6FA] text-[#58779D]">
                        <svg xmlns="http://www.w3.org/2000/svg" class="h-7 w-7" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.5">
                            <rect x="3" y="5" width="18" height="14" rx="2" />
                            <path stroke-linecap="round" d="M3 10h18M7 15h3" />
                        </svg>
                    </div>
                    <h3 class="mt-4 font-bold text-[#101D35]">No payments recorded</h3>
                    <p class="mt-1 text-sm text-slate-500">Payment entries will appear here when available.</p>
                </div>

                <div v-else class="overflow-x-auto">
                    <table class="w-full min-w-[700px] text-left">
                        <thead class="bg-[#F4F6FA]">
                            <tr class="text-xs font-semibold uppercase tracking-wider text-slate-500">
                                <th class="px-5 py-4">Payment</th>
                                <th class="px-5 py-4">Amount</th>
                                <th class="px-5 py-4">Method</th>
                                <th class="px-5 py-4">Status</th>
                                <th class="px-5 py-4">Processed</th>
                            </tr>
                        </thead>
                        <tbody class="divide-y divide-[#E8EDF4]">
                            <tr v-for="payment in reservation.payments" :key="payment.id" class="transition hover:bg-[#F8FAFD]">
                                <td class="px-5 py-4">
                                    <p class="font-semibold text-[#101D35]">{{ payment.payment_number || `Payment #${payment.id}` }}</p>
                                </td>
                                <td class="px-5 py-4 font-bold text-[#101D35]">{{ formatMoney(payment.amount) }}</td>
                                <td class="px-5 py-4 text-sm capitalize text-[#263F60]">{{ payment.payment_method || '—' }}</td>
                                <td class="px-5 py-4">
                                    <span class="inline-flex rounded-full border border-[#DCE3ED] bg-[#F4F6FA] px-3 py-1 text-xs font-semibold capitalize text-[#263F60]">
                                        {{ payment.status || 'Unknown' }}
                                    </span>
                                </td>
                                <td class="px-5 py-4 text-sm text-slate-500">{{ formatDateTime(payment.processed_at) }}</td>
                            </tr>
                        </tbody>
                    </table>
                </div>
            </section>
        </main>
    </AdminLayout>
</template>
