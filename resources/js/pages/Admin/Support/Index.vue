```vue
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, router } from '@inertiajs/vue3';
import { computed, ref, watch } from 'vue';
import { index, show } from '@/routes/admin/support';

const props = defineProps<{
    tickets: {
        data: Array<{
            id: number;
            subject: string;
            message: string;
            status: string;
            user?: { id: number; name: string; email: string };
            guest_name?: string;
            guest_email?: string;
            created_at: string;
            updated_at: string;
        }>;
        links: Array<{ url: string | null; label: string; active: boolean }>;
    };
    filters: {
        search?: string;
        status?: string;
        type?: 'customer' | 'guest';
    };
    statuses: Record<string, { label: string; color: string }>;
    statusCounts: {
        customer: Record<string, number>;
        guest: Record<string, number>;
    };
}>();

const search = ref(props.filters?.search || '');
const statusFilter = ref(props.filters?.status || 'all');
const ticketType = ref<'customer' | 'guest'>(
    props.filters?.type || 'customer',
);

const statusColors = computed(() => {
    const colors: Record<string, { bg: string; text: string; dot: string }> = {};

    for (const [status, data] of Object.entries(props.statuses || {})) {
        const hex = data.color?.replace('#', '') || '6B7280';
        const r = parseInt(hex.substring(0, 2), 16);
        const g = parseInt(hex.substring(2, 4), 16);
        const b = parseInt(hex.substring(4, 6), 16);

        colors[status] = {
            bg: `rgba(${r}, ${g}, ${b}, 0.12)`,
            text: data.color || '#6B7280',
            dot: data.color || '#6B7280',
        };
    }

    return colors;
});

const getStatusColor = (status: string) =>
    statusColors.value[status] || {
        bg: 'rgba(107, 114, 128, 0.1)',
        text: '#6B7280',
        dot: '#6B7280',
    };

const getStatusCount = (
    type: 'customer' | 'guest',
    status: string,
): number => props.statusCounts?.[type]?.[status] || 0;

const getTotalCount = (type: 'customer' | 'guest'): number =>
    props.statusCounts?.[type]?.all || 0;

const doSearch = () => {
    router.get(
        index().url,
        {
            search: search.value || null,
            status: statusFilter.value === 'all' ? null : statusFilter.value,
            type: ticketType.value,
        },
        {
            preserveState: true,
            preserveScroll: true,
            replace: true,
        },
    );
};

watch(search, (newVal, oldVal) => {
    if (newVal === '' && oldVal !== '') doSearch();
});

watch(ticketType, () => {
    doSearch();
});

const formatDate = (dateString: string) =>
    new Date(dateString).toLocaleDateString('en-IN', {
        day: 'numeric',
        month: 'short',
        year: 'numeric',
        hour: '2-digit',
        minute: '2-digit',
    });

function goToTicket(id: number) {
    router.visit(show(id).url);
}
</script>

<template>
    <Head title="Support Tickets" />

    <AdminLayout>
        <main class="min-h-full space-y-6 bg-[#F4F6FA] px-4 py-6 sm:px-6 lg:px-8">
            <!-- Page header -->
            <section class="flex flex-col gap-4 rounded-2xl bg-[#101D35] p-6 text-white shadow-lg sm:flex-row sm:items-center sm:justify-between">
                <div>
                    <p class="mb-2 text-xs font-semibold uppercase tracking-[0.25em] text-[#C5D0DE]">
                        Customer care
                    </p>
                    <h1 class="text-2xl font-bold sm:text-3xl">
                        Support Tickets
                    </h1>
                    <p class="mt-2 text-sm text-slate-300">
                        Manage customer questions and guest support requests.
                    </p>
                </div>

                <div class="rounded-xl border border-[#58779D] bg-[#263F60] px-5 py-4">
                    <p class="text-sm text-[#C5D0DE]">Total tickets</p>
                    <p class="mt-1 text-3xl font-bold">
                        {{ getTotalCount(ticketType) }}
                    </p>
                </div>
            </section>

            <!-- Ticket type selector -->
            <section class="grid grid-cols-1 gap-4 sm:grid-cols-2">
                <label class="cursor-pointer">
                    <input
                        v-model="ticketType"
                        type="radio"
                        value="customer"
                        class="peer sr-only"
                    />
                    <div
                        class="flex items-center justify-between rounded-2xl border bg-white p-5 shadow-sm transition hover:shadow-md"
                        :class="ticketType === 'customer'
                            ? 'border-[#263F60] ring-2 ring-[#263F60]/15'
                            : 'border-[#DCE3ED]'"
                    >
                        <div class="flex items-center gap-4">
                            <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M20 11.5a7.5 7.5 0 0 1-11.2 6.5L4 19l1.1-4.2A7.5 7.5 0 1 1 20 11.5Z"/>
                                </svg>
                            </span>
                            <div>
                                <p class="font-bold text-[#101D35]">Customer Tickets</p>
                                <p class="mt-1 text-sm text-slate-500">Registered customers</p>
                            </div>
                        </div>
                        <span class="rounded-full bg-[#EEF2F7] px-3 py-1.5 text-sm font-bold text-[#263F60]">
                            {{ getTotalCount('customer') }}
                        </span>
                    </div>
                </label>

                <label class="cursor-pointer">
                    <input
                        v-model="ticketType"
                        type="radio"
                        value="guest"
                        class="peer sr-only"
                    />
                    <div
                        class="flex items-center justify-between rounded-2xl border bg-white p-5 shadow-sm transition hover:shadow-md"
                        :class="ticketType === 'guest'
                            ? 'border-[#263F60] ring-2 ring-[#263F60]/15'
                            : 'border-[#DCE3ED]'"
                    >
                        <div class="flex items-center gap-4">
                            <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M16 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2m6-10a4 4 0 1 0 0-8 4 4 0 0 0 0 8Zm8-7a4 4 0 0 1 0 8m2 2a4 4 0 0 1 2 3v2"/>
                                </svg>
                            </span>
                            <div>
                                <p class="font-bold text-[#101D35]">Guest Tickets</p>
                                <p class="mt-1 text-sm text-slate-500">Visitors without an account</p>
                            </div>
                        </div>
                        <span class="rounded-full bg-[#EEF2F7] px-3 py-1.5 text-sm font-bold text-[#263F60]">
                            {{ getTotalCount('guest') }}
                        </span>
                    </div>
                </label>
            </section>

            <!-- Search and filters -->
            <section class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm sm:p-6">
                <div class="mb-4">
                    <h2 class="font-bold text-[#101D35]">Find a ticket</h2>
                    <p class="mt-1 text-sm text-slate-500">
                        Search tickets or filter them by status.
                    </p>
                </div>

                <form class="flex flex-col gap-3 sm:flex-row" @submit.prevent="doSearch">
                    <div class="relative flex-1">
                        <svg class="absolute top-1/2 left-3 h-5 w-5 -translate-y-1/2 text-[#58779D]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <circle cx="11" cy="11" r="7" stroke-width="1.8"/>
                            <path stroke-linecap="round" stroke-width="1.8" d="m16 16 4 4"/>
                        </svg>
                        <input
                            v-model="search"
                            :placeholder="`Search ${ticketType} tickets...`"
                            class="w-full rounded-xl border border-[#DCE3ED] bg-[#F8FAFC] py-3 pr-4 pl-10 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-2 focus:ring-[#58779D]/15"
                        />
                    </div>

                    <button
                        type="submit"
                        class="rounded-xl bg-[#101D35] px-6 py-3 text-sm font-semibold text-white transition hover:bg-[#263F60]"
                    >
                        Search tickets
                    </button>
                </form>

                <div class="mt-5">
                    <p class="mb-3 text-xs font-semibold uppercase tracking-wider text-slate-500">
                        Filter by status
                    </p>

                    <div class="flex flex-wrap gap-2">
                        <label class="cursor-pointer">
                            <input v-model="statusFilter" type="radio" value="all" class="sr-only" @change="doSearch"/>
                            <span
                                class="inline-flex rounded-full border px-3.5 py-2 text-sm font-medium transition"
                                :class="statusFilter === 'all'
                                    ? 'border-[#101D35] bg-[#101D35] text-white'
                                    : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                            >
                                All ({{ getTotalCount(ticketType) }})
                            </span>
                        </label>

                        <label v-for="(status, key) in statuses" :key="key" class="cursor-pointer">
                            <input v-model="statusFilter" type="radio" :value="key" class="sr-only" @change="doSearch"/>
                            <span
                                class="inline-flex items-center gap-2 rounded-full border px-3.5 py-2 text-sm font-medium transition"
                                :class="statusFilter === key
                                    ? 'border-[#263F60] bg-[#E5EBF3] text-[#101D35]'
                                    : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                            >
                                <span class="h-2 w-2 rounded-full" :style="{ backgroundColor: status.color }"></span>
                                {{ status.label }} ({{ getStatusCount(ticketType, key) }})
                            </span>
                        </label>
                    </div>
                </div>
            </section>

            <!-- Tickets table -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex items-center justify-between border-b border-[#E5EAF1] px-5 py-4 sm:px-6">
                    <div>
                        <h2 class="font-bold text-[#101D35]">Ticket list</h2>
                        <p class="mt-1 text-sm text-slate-500">
                            {{ ticketType === 'customer' ? 'Registered customer requests' : 'Guest requests' }}
                        </p>
                    </div>
                    <span class="rounded-lg bg-[#EEF2F7] px-3 py-2 text-xs font-semibold text-[#263F60]">
                        {{ props.tickets.data.length }} shown
                    </span>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-[760px] w-full text-left">
                        <thead class="bg-[#101D35] text-xs font-semibold uppercase tracking-wider text-[#C5D0DE]">
                            <tr>
                                <th class="px-5 py-4">Ticket</th>
                                <th class="px-5 py-4">{{ ticketType === 'customer' ? 'Customer' : 'Guest' }}</th>
                                <th class="px-5 py-4">Subject</th>
                                <th v-if="ticketType === 'customer'" class="px-5 py-4">Status</th>
                                <th class="px-5 py-4">Created</th>
                                <th class="px-5 py-4">Action</th>
                            </tr>
                        </thead>

                        <tbody class="divide-y divide-[#E5EAF1]">
                            <tr
                                v-for="ticket in props.tickets.data"
                                :key="ticket.id"
                                class="cursor-pointer transition hover:bg-[#F4F6FA]"
                                @click="goToTicket(ticket.id)"
                            >
                                <td class="px-5 py-4">
                                    <span class="font-bold text-[#263F60]">#{{ ticket.id }}</span>
                                </td>

                                <td class="px-5 py-4">
                                    <p class="font-semibold text-[#101D35]">
                                        {{ ticketType === 'customer' ? ticket.user?.name : ticket.guest_name }}
                                    </p>
                                    <p class="mt-1 text-xs text-slate-500">
                                        {{ ticketType === 'customer' ? ticket.user?.email : ticket.guest_email }}
                                    </p>
                                </td>

                                <td class="max-w-xs px-5 py-4">
                                    <p class="font-semibold text-[#101D35]">{{ ticket.subject }}</p>
                                    <p class="mt-1 line-clamp-2 text-sm text-slate-500">{{ ticket.message }}</p>
                                </td>

                                <td v-if="ticketType === 'customer'" class="px-5 py-4">
                                    <span
                                        class="inline-flex items-center gap-2 rounded-full px-3 py-1.5 text-xs font-semibold"
                                        :style="{
                                            backgroundColor: getStatusColor(ticket.status).bg,
                                            color: getStatusColor(ticket.status).text,
                                        }"
                                    >
                                        <span class="h-2 w-2 rounded-full" :style="{ backgroundColor: getStatusColor(ticket.status).dot }"></span>
                                        {{ statuses[ticket.status]?.label || ticket.status }}
                                    </span>
                                </td>

                                <td class="whitespace-nowrap px-5 py-4 text-sm text-slate-500">
                                    {{ formatDate(ticket.created_at) }}
                                </td>

                                <td class="px-5 py-4">
                                    <button
                                        type="button"
                                        class="inline-flex items-center gap-1 rounded-lg border border-[#DCE3ED] px-3 py-2 text-xs font-semibold text-[#263F60] transition hover:border-[#263F60] hover:bg-[#E5EBF3]"
                                        @click.stop="goToTicket(ticket.id)"
                                    >
                                        View
                                        <span aria-hidden="true">→</span>
                                    </button>
                                </td>
                            </tr>

                            <tr v-if="props.tickets.data.length === 0">
                                <td :colspan="ticketType === 'customer' ? 6 : 5" class="px-5 py-16 text-center">
                                    <div class="mx-auto flex h-14 w-14 items-center justify-center rounded-2xl bg-[#E5EBF3] text-[#263F60]">
                                        <svg class="h-7 w-7" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M8 10h8M8 14h5m-8 6 1.5-3H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h14a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2H9l-4 3Z"/>
                                        </svg>
                                    </div>
                                    <p class="mt-4 font-semibold text-[#101D35]">No tickets found</p>
                                    <p class="mt-1 text-sm text-slate-500">Try another search term or status filter.</p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Pagination -->
                <nav
                    v-if="props.tickets.links?.length > 3"
                    class="flex flex-wrap items-center justify-center gap-2 border-t border-[#E5EAF1] bg-[#F8FAFC] px-4 py-4"
                    aria-label="Pagination"
                >
                    <template v-for="(link, i) in props.tickets.links" :key="i">
                        <button
                            v-if="link.url"
                            type="button"
                            class="rounded-lg border px-3 py-2 text-sm transition"
                            :class="link.active
                                ? 'border-[#101D35] bg-[#101D35] font-semibold text-white'
                                : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#E5EBF3]'"
                            @click="router.visit(link.url, { preserveScroll: true, preserveState: true })"
                            v-html="link.label"
                        ></button>
                        <span
                            v-else
                            class="rounded-lg px-3 py-2 text-sm text-slate-400"
                            v-html="link.label"
                        ></span>
                    </template>
                </nav>
            </section>
        </main>
    </AdminLayout>
</template>
```