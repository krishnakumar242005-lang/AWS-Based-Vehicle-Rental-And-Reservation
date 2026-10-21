```vue
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link, router } from '@inertiajs/vue3';
import { ref, watch, computed } from 'vue';
import { show, index } from '@/routes/admin/clients';

const props = defineProps<{
    clients: {
        data: Array<{
            id: number;
            name: string;
            email: string;
            is_active: boolean;
            reservations_count: number;
            payments_count: number;
            created_at?: string;
        }>;
        links: Array<{ url: string | null; label: string; active: boolean }>;
    };
    filters: {
        search?: string;
        status?: string;
    };
    statuses: Record<string, { label: string; count: number; color: string }>;
}>();

const search = ref(props.filters?.search || '');
const statusFilter = ref(props.filters?.status || 'all');

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

const totalClients = computed(() =>
    Object.values(props.statuses || {}).reduce(
        (total, status) => total + status.count,
        0,
    ),
);

const activeClients = computed(
    () => props.clients.data.filter((client) => client.is_active).length,
);

const suspendedClients = computed(
    () => props.clients.data.filter((client) => !client.is_active).length,
);

const doSearch = () => {
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
};

watch(search, (newValue, oldValue) => {
    if (newValue === '' && oldValue !== '') doSearch();
});

const navigateToClient = (id: number) => {
    router.visit(show(id).url);
};
</script>

<template>
    <Head title="Clients" />

    <AdminLayout>
        <main class="min-h-full space-y-6 bg-[#F4F6FA] px-4 py-6 sm:px-6 lg:px-8">
            <!-- Header -->
            <section class="flex flex-col gap-4 rounded-2xl bg-[#101D35] p-6 text-white shadow-lg sm:flex-row sm:items-center sm:justify-between">
                <div>
                    <p class="mb-2 text-xs font-semibold uppercase tracking-[0.25em] text-[#C5D0DE]">
                        Customer management
                    </p>
                    <h1 class="text-2xl font-bold sm:text-3xl">Clients</h1>
                    <p class="mt-2 text-sm text-slate-300">
                        Manage client accounts and view their rental activity.
                    </p>
                </div>

                <div class="rounded-xl border border-[#58779D] bg-[#263F60] px-5 py-4">
                    <p class="text-sm text-[#C5D0DE]">Total clients</p>
                    <p class="mt-1 text-3xl font-bold">{{ totalClients }}</p>
                </div>
            </section>

            <!-- Summary cards -->
            <section class="grid grid-cols-1 gap-4 sm:grid-cols-3">
                <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center justify-between gap-3">
                        <div>
                            <p class="text-sm font-medium text-slate-500">Clients in current page</p>
                            <p class="mt-2 text-3xl font-bold text-[#101D35]">
                                {{ props.clients.data.length }}
                            </p>
                        </div>
                        <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                            <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M16 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2m6-10a4 4 0 1 0 0-8 4 4 0 0 0 0 8Zm8-7a4 4 0 0 1 0 8m2 2a4 4 0 0 1 2 3v2"/>
                            </svg>
                        </span>
                    </div>
                </article>

                <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center justify-between gap-3">
                        <div>
                            <p class="text-sm font-medium text-slate-500">Active on current page</p>
                            <p class="mt-2 text-3xl font-bold text-[#101D35]">{{ activeClients }}</p>
                        </div>
                        <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                            <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="m5 12 4 4L19 6"/>
                            </svg>
                        </span>
                    </div>
                </article>

                <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center justify-between gap-3">
                        <div>
                            <p class="text-sm font-medium text-slate-500">Suspended on current page</p>
                            <p class="mt-2 text-3xl font-bold text-[#101D35]">{{ suspendedClients }}</p>
                        </div>
                        <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                            <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <circle cx="12" cy="12" r="9" stroke-width="1.8"/>
                                <path stroke-linecap="round" stroke-width="1.8" d="m6 6 12 12"/>
                            </svg>
                        </span>
                    </div>
                </article>
            </section>

            <!-- Search and filters -->
            <section class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm sm:p-6">
                <h2 class="font-bold text-[#101D35]">Find a client</h2>
                <p class="mt-1 text-sm text-slate-500">
                    Search by client name or email and filter by account status.
                </p>

                <form class="mt-4 flex flex-col gap-3 sm:flex-row" @submit.prevent="doSearch">
                    <div class="relative flex-1">
                        <svg class="absolute top-1/2 left-3 h-5 w-5 -translate-y-1/2 text-[#58779D]" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                            <circle cx="11" cy="11" r="7" stroke-width="1.8"/>
                            <path stroke-linecap="round" stroke-width="1.8" d="m16 16 4 4"/>
                        </svg>
                        <input
                            v-model="search"
                            type="search"
                            placeholder="Search name or email..."
                            class="w-full rounded-xl border border-[#DCE3ED] bg-[#F8FAFC] py-3 pr-4 pl-10 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-2 focus:ring-[#58779D]/15"
                        />
                    </div>

                    <button
                        type="submit"
                        class="rounded-xl bg-[#101D35] px-6 py-3 text-sm font-semibold text-white transition hover:bg-[#263F60]"
                    >
                        Search clients
                    </button>
                </form>

                <div class="mt-5">
                    <p class="mb-3 text-xs font-semibold uppercase tracking-wider text-slate-500">
                        Account status
                    </p>

                    <div class="flex flex-wrap gap-2">
                        <label class="cursor-pointer">
                            <input
                                v-model="statusFilter"
                                type="radio"
                                value="all"
                                class="sr-only"
                                @change="doSearch"
                            />
                            <span
                                class="inline-flex rounded-full border px-4 py-2 text-sm font-medium transition"
                                :class="statusFilter === 'all'
                                    ? 'border-[#101D35] bg-[#101D35] text-white'
                                    : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                            >
                                All ({{ totalClients }})
                            </span>
                        </label>

                        <label
                            v-for="(status, key) in statuses"
                            :key="key"
                            class="cursor-pointer"
                        >
                            <input
                                v-model="statusFilter"
                                type="radio"
                                :value="key"
                                class="sr-only"
                                @change="doSearch"
                            />
                            <span
                                class="inline-flex items-center gap-2 rounded-full border px-4 py-2 text-sm font-medium transition"
                                :class="statusFilter === key
                                    ? 'border-[#263F60] bg-[#E5EBF3] text-[#101D35]'
                                    : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                            >
                                <span
                                    class="h-2 w-2 rounded-full"
                                    :style="{ backgroundColor: status.color }"
                                />
                                {{ status.label }} ({{ status.count }})
                            </span>
                        </label>
                    </div>
                </div>
            </section>

            <!-- Clients table -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex items-center justify-between gap-3 border-b border-[#E5EAF1] px-5 py-4 sm:px-6">
                    <div>
                        <h2 class="font-bold text-[#101D35]">Client directory</h2>
                        <p class="mt-1 text-sm text-slate-500">
                            Account details and rental activity
                        </p>
                    </div>
                    <span class="rounded-lg bg-[#EEF2F7] px-3 py-2 text-xs font-semibold text-[#263F60]">
                        {{ props.clients.data.length }} shown
                    </span>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-[760px] w-full text-left">
                        <thead class="bg-[#101D35] text-xs font-semibold uppercase tracking-wider text-[#C5D0DE]">
                            <tr>
                                <th class="px-5 py-4">Client</th>
                                <th class="px-5 py-4">Reservations</th>
                                <th class="px-5 py-4">Payments</th>
                                <th class="px-5 py-4">Status</th>
                                <th class="px-5 py-4 text-right">Action</th>
                            </tr>
                        </thead>

                        <tbody class="divide-y divide-[#E5EAF1]">
                            <tr
                                v-for="client in props.clients.data"
                                :key="client.id"
                                class="cursor-pointer transition hover:bg-[#F4F6FA]"
                                @click="navigateToClient(client.id)"
                            >
                                <td class="px-5 py-4">
                                    <div class="flex items-center gap-3">
                                        <span class="flex h-10 w-10 shrink-0 items-center justify-center rounded-xl bg-[#E5EBF3] font-bold text-[#263F60]">
                                            {{ client.name.trim().charAt(0).toUpperCase() }}
                                        </span>
                                        <div class="min-w-0">
                                            <p class="font-semibold text-[#101D35]">{{ client.name }}</p>
                                            <p class="mt-1 break-all text-xs text-slate-500">{{ client.email }}</p>
                                        </div>
                                    </div>
                                </td>

                                <td class="px-5 py-4">
                                    <span class="inline-flex min-w-10 justify-center rounded-lg bg-[#EEF2F7] px-3 py-2 font-semibold text-[#263F60]">
                                        {{ client.reservations_count }}
                                    </span>
                                </td>

                                <td class="px-5 py-4">
                                    <span class="inline-flex min-w-10 justify-center rounded-lg bg-[#EEF2F7] px-3 py-2 font-semibold text-[#263F60]">
                                        {{ client.payments_count }}
                                    </span>
                                </td>

                                <td class="px-5 py-4">
                                    <span
                                        class="inline-flex items-center gap-2 rounded-full px-3 py-1.5 text-xs font-semibold"
                                        :style="{
                                            backgroundColor: getStatusColor(client.is_active ? 'active' : 'suspended').bg,
                                            color: getStatusColor(client.is_active ? 'active' : 'suspended').text,
                                        }"
                                    >
                                        <span
                                            class="h-2 w-2 rounded-full"
                                            :style="{ backgroundColor: getStatusColor(client.is_active ? 'active' : 'suspended').dot }"
                                        />
                                        {{ client.is_active ? 'Active' : 'Suspended' }}
                                    </span>
                                </td>

                                <td class="px-5 py-4 text-right">
                                    <Link
                                        :href="show(client.id).url"
                                        class="inline-flex items-center gap-2 rounded-xl border border-[#DCE3ED] px-4 py-2 text-sm font-semibold text-[#263F60] transition hover:border-[#263F60] hover:bg-[#E5EBF3]"
                                        @click.stop
                                    >
                                        View details
                                        <span aria-hidden="true">→</span>
                                    </Link>
                                </td>
                            </tr>

                            <tr v-if="props.clients.data.length === 0">
                                <td colspan="5" class="px-5 py-16 text-center">
                                    <div class="mx-auto flex h-14 w-14 items-center justify-center rounded-2xl bg-[#E5EBF3] text-[#263F60]">
                                        <svg class="h-7 w-7" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                            <circle cx="10" cy="10" r="7" stroke-width="1.8"/>
                                            <path stroke-linecap="round" stroke-width="1.8" d="m15 15 5 5"/>
                                        </svg>
                                    </div>
                                    <p class="mt-4 font-semibold text-[#101D35]">No clients found</p>
                                    <p class="mt-1 text-sm text-slate-500">
                                        Try another name, email, or account status.
                                    </p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Pagination -->
                <nav
                    v-if="props.clients.links?.length"
                    class="flex flex-wrap items-center justify-center gap-2 border-t border-[#E5EAF1] bg-[#F8FAFC] px-4 py-4"
                    aria-label="Client pagination"
                >
                    <Link
                        v-for="(link, i) in props.clients.links"
                        :key="i"
                        :href="link.url || ''"
                        preserve-scroll
                        :class="[
                            'rounded-lg border px-3 py-2 text-sm transition',
                            link.active
                                ? 'border-[#101D35] bg-[#101D35] font-semibold text-white'
                                : 'border-[#DCE3ED] bg-white text-[#263F60] hover:bg-[#E5EBF3]',
                            !link.url && 'pointer-events-none opacity-50',
                        ]"
                    >
                        <span v-html="link.label" />
                    </Link>
                </nav>
            </section>
        </main>
    </AdminLayout>
</template>
```