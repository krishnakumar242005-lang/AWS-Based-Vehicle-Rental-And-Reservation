```vue
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link } from '@inertiajs/vue3';
import { computed } from 'vue';

type PaymentStatus = {
    label: string;
    count: number;
    color: string;
};

type Payment = {
    id: number;
    payment_number: string;
    amount: number | string;
    currency?: string;
    payment_method: string;
    status: string;
    processed_at?: string | null;
    user?: { id: number; name: string; email: string } | null;
    reservation?: { id: number; reservation_number: string } | null;
};

const props = defineProps<{
    payments: {
        data: Payment[];
        links: Array<{ url: string | null; label: string; active: boolean }>;
    };
    statuses: Record<string, PaymentStatus>;
    currency: { symbol: string; code: string };
}>();

function fmtMoney(amount?: number | string) {
    const value = Number(amount ?? 0);

    return new Intl.NumberFormat('en-IN', {
        style: 'currency',
        currency: 'INR',
        minimumFractionDigits: 2,
        maximumFractionDigits: 2,
    }).format(value);
}

const statusColors = computed(() => {
    const colors: Record<string, { bg: string; text: string; dot: string }> = {};

    for (const [key, status] of Object.entries(props.statuses ?? {})) {
        const color = status.color || '#64748B';
        const hex = color.replace('#', '');

        if (/^[0-9a-fA-F]{6}$/.test(hex)) {
            const r = parseInt(hex.slice(0, 2), 16);
            const g = parseInt(hex.slice(2, 4), 16);
            const b = parseInt(hex.slice(4, 6), 16);

            colors[key] = {
                bg: `rgba(${r}, ${g}, ${b}, 0.12)`,
                text: color,
                dot: color,
            };
        } else {
            colors[key] = {
                bg: 'rgba(100, 116, 139, 0.12)',
                text: '#64748B',
                dot: '#64748B',
            };
        }
    }

    return colors;
});

function getStatusColor(status: string) {
    return statusColors.value[status] ?? {
        bg: 'rgba(100, 116, 139, 0.12)',
        text: '#64748B',
        dot: '#64748B',
    };
}

const totalPayments = computed(() => props.payments.data.length);

const successfulPayments = computed(() =>
    props.payments.data.filter((payment) =>
        ['completed', 'successful', 'succeeded', 'paid'].includes(
            payment.status.toLowerCase(),
        ),
    ).length,
);

const totalAmount = computed(() =>
    props.payments.data.reduce(
        (total, payment) => total + Number(payment.amount ?? 0),
        0,
    ),
);
</script>

<template>
    <Head title="Payments" />

    <AdminLayout>
        <main class="min-h-screen space-y-7 bg-[#F4F6FA] p-4 sm:p-6 lg:p-8">
            <header>
                <p class="text-sm font-semibold uppercase tracking-[0.2em] text-[#58779D]">
                    Financial Management
                </p>
                <h1 class="mt-2 text-2xl font-bold tracking-tight text-[#101D35] sm:text-3xl">
                    Payments
                </h1>
                <p class="mt-2 text-sm text-slate-500">
                    Review payment transactions and their associated reservations.
                </p>
            </header>

            <section class="grid grid-cols-1 gap-4 sm:grid-cols-2 xl:grid-cols-3">
                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <p class="text-sm font-medium text-slate-500">Payments on This Page</p>
                    <p class="mt-2 text-3xl font-bold text-[#101D35]">
                        {{ totalPayments }}
                    </p>
                    <p class="mt-3 text-xs text-slate-400">Transactions in the current page</p>
                </div>

                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <p class="text-sm font-medium text-slate-500">Successful Payments</p>
                    <p class="mt-2 text-3xl font-bold text-[#263F60]">
                        {{ successfulPayments }}
                    </p>
                    <p class="mt-3 text-xs text-slate-400">
                        Completed or paid transactions on this page
                    </p>
                </div>

                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <p class="text-sm font-medium text-slate-500">Amount on This Page</p>
                    <p class="mt-2 break-words text-2xl font-bold text-[#101D35] sm:text-3xl">
                        {{ fmtMoney(totalAmount) }}
                    </p>
                    <p class="mt-3 text-xs text-slate-400">Sum of listed payment amounts</p>
                </div>
            </section>

            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex flex-col justify-between gap-2 border-b border-[#DCE3ED] px-5 py-5 sm:flex-row sm:items-center sm:px-6">
                    <div>
                        <h2 class="text-lg font-bold text-[#101D35]">Transaction History</h2>
                        <p class="mt-1 text-sm text-slate-500">
                            Payment details, clients, and reservation references.
                        </p>
                    </div>
                    <span class="w-fit rounded-lg bg-[#E8EDF4] px-3 py-1.5 text-xs font-semibold text-[#263F60]">
                        {{ totalPayments }} listed
                    </span>
                </div>

                <div class="overflow-x-auto">
                    <table class="w-full min-w-[950px] divide-y divide-[#E5EAF1]">
                        <thead class="bg-[#101D35]">
                            <tr>
                                <th
                                    v-for="heading in ['Payment #', 'Client', 'Reservation', 'Amount', 'Method', 'Status', 'Processed At']"
                                    :key="heading"
                                    class="px-5 py-4 text-left text-xs font-semibold uppercase tracking-wider text-[#C5D0DE]"
                                >
                                    {{ heading }}
                                </th>
                            </tr>
                        </thead>

                        <tbody class="divide-y divide-[#E5EAF1]">
                            <tr
                                v-for="p in payments.data"
                                :key="p.id"
                                class="transition hover:bg-[#F4F6FA]"
                            >
                                <td class="px-5 py-4 font-semibold text-[#263F60]">
                                    {{ p.payment_number }}
                                </td>

                                <td class="px-5 py-4">
                                    <div class="font-semibold text-[#101D35]">
                                        {{ p.user?.name || '—' }}
                                    </div>
                                    <div class="mt-1 text-xs text-slate-500">
                                        {{ p.user?.email || '' }}
                                    </div>
                                </td>

                                <td class="px-5 py-4">
                                    <Link
                                        v-if="p.reservation"
                                        :href="`/admin/reservations/${p.reservation.id}`"
                                        class="font-semibold text-[#58779D] hover:text-[#101D35] hover:underline"
                                    >
                                        {{ p.reservation.reservation_number }} ↗
                                    </Link>
                                    <span v-else class="text-slate-400">—</span>
                                </td>

                                <td class="px-5 py-4">
                                    <span class="font-bold text-[#263F60]">
                                        {{ fmtMoney(p.amount) }}
                                    </span>
                                    <span class="mt-1 block text-xs text-slate-500">INR</span>
                                </td>

                                <td class="px-5 py-4">
                                    <span class="inline-flex rounded-lg border border-[#DCE3ED] bg-[#F4F6FA] px-2.5 py-1.5 text-xs font-medium capitalize text-[#263F60]">
                                        {{ p.payment_method.replace(/_/g, ' ') }}
                                    </span>
                                </td>

                                <td class="px-5 py-4">
                                    <span
                                        class="inline-flex items-center gap-2 rounded-full px-3 py-1.5 text-xs font-semibold capitalize"
                                        :style="{
                                            backgroundColor: getStatusColor(p.status).bg,
                                            color: getStatusColor(p.status).text,
                                        }"
                                    >
                                        <span
                                            class="h-2 w-2 rounded-full"
                                            :style="{ backgroundColor: getStatusColor(p.status).dot }"
                                        ></span>
                                        {{ p.status.replace(/_/g, ' ') }}
                                    </span>
                                </td>

                                <td class="px-5 py-4 text-sm text-slate-600">
                                    {{ p.processed_at ? new Date(p.processed_at).toLocaleString() : '—' }}
                                </td>
                            </tr>

                            <tr v-if="payments.data.length === 0">
                                <td colspan="7" class="px-5 py-16 text-center">
                                    <div class="text-2xl text-[#263F60]">₹</div>
                                    <h3 class="mt-4 font-bold text-[#101D35]">No payments found</h3>
                                    <p class="mt-1 text-sm text-slate-500">
                                        Payment transactions will appear here when available.
                                    </p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <nav
                    v-if="payments.links?.length"
                    aria-label="Payment pagination"
                    class="flex flex-wrap items-center justify-center gap-2 border-t border-[#DCE3ED] bg-[#F8FAFC] px-4 py-4"
                >
                    <template v-for="(link, i) in payments.links" :key="i">
                        <Link
                            v-if="link.url"
                            :href="link.url"
                            preserve-scroll
                            :class="[
                                'inline-flex min-w-9 items-center justify-center rounded-lg border px-3 py-2 text-sm font-medium transition',
                                link.active
                                    ? 'border-[#101D35] bg-[#101D35] text-white'
                                    : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#E8EDF4]',
                            ]"
                        >
                            <span v-html="link.label"></span>
                        </Link>
                        <span
                            v-else
                            class="inline-flex min-w-9 cursor-not-allowed items-center justify-center rounded-lg border border-[#E5EAF1] bg-[#F4F6FA] px-3 py-2 text-sm text-slate-400"
                        >
                            <span v-html="link.label"></span>
                        </span>
                    </template>
                </nav>
            </section>
        </main>
    </AdminLayout>
</template>
