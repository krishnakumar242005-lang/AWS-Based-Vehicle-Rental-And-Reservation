```vue
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link, router } from '@inertiajs/vue3';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { ref, watch, computed } from 'vue';
import {
    Dialog,
    DialogClose,
    DialogContent,
    DialogDescription,
    DialogFooter,
    DialogHeader,
    DialogTitle,
} from '@/components/ui/dialog';
import { AlertCircle, CarFront, Search, Plus, SlidersHorizontal, CalendarDays, Pencil, Trash2 } from 'lucide-vue-next';
import { Alert, AlertDescription } from '@/components/ui/alert';
import { create, index, destroy } from '@/routes/admin/cars';

const props = defineProps<{
    cars: {
        data: Array<{
            id: number;
            make: string;
            model: string;
            year: number;
            license_plate: string;
            price_per_day: string | number;
            status: string;
            status_label?: string;
            status_color?: string;
            image_url?: string;
        }>;
        links: Array<{ url: string | null; label: string; active: boolean }>;
    };
    filters: {
        search?: string;
        status?: string;
    };
    statuses: Record<string, {
        label: string;
        count: number;
        color: string;
    }>;
    currency: { symbol: string; code: string };
}>();

const statusColors = computed(() => {
    const colors: Record<string, { bg: string; text: string; dot: string }> = {};

    for (const [status, data] of Object.entries(props.statuses || {})) {
        const hex = data.color.replace('#', '');
        const r = parseInt(hex.substring(0, 2), 16);
        const g = parseInt(hex.substring(2, 4), 16);
        const b = parseInt(hex.substring(4, 6), 16);

        colors[status] = {
            bg: `rgba(${r}, ${g}, ${b}, 0.12)`,
            text: data.color,
            dot: data.color,
        };
    }

    return colors;
});

const getStatusColor = (status: string) =>
    statusColors.value[status] || {
        bg: '#F1F5F9',
        text: '#475569',
        dot: '#64748B',
    };

const search = ref(props.filters?.search || '');
const statusFilter = ref(props.filters?.status || 'all');

function doSearch() {
    router.get(index(), {
        search: search.value,
        status: statusFilter.value === 'all' ? null : statusFilter.value,
    }, {
        preserveState: true,
        replace: true,
    });
}

watch(search, (value, oldValue) => {
    if (value === '' && oldValue !== '') doSearch();
});

const showDeleteDialog = ref(false);
const carToDelete = ref<number | null>(null);

const openDeleteDialog = (id: number) => {
    carToDelete.value = id;
    showDeleteDialog.value = true;
};

const destroyCar = () => {
    if (!carToDelete.value) return;

    router.delete(destroy(carToDelete.value).url, {
        preserveScroll: true,
        onSuccess: () => {
            showDeleteDialog.value = false;
            carToDelete.value = null;
        },
    });
};
</script>

<template>
    <Head title="Manage Cars" />

    <AdminLayout>
        <main class="min-w-0 flex-1 space-y-7 bg-[#F4F6FA] p-4 sm:p-6 lg:p-8">
            <!-- Page heading -->
            <section class="flex flex-col justify-between gap-4 sm:flex-row sm:items-center">
                <div>
                    <div class="mb-2 flex items-center gap-2 text-sm text-[#58779D]">
                        <CarFront class="h-4 w-4" />
                        <span>Fleet management</span>
                    </div>
                    <h1 class="text-3xl font-bold tracking-tight text-[#101D35]">
                        Manage Cars
                    </h1>
                    <p class="mt-2 text-sm text-slate-500">
                        View, search and manage your vehicle inventory.
                    </p>
                </div>

                <Link :href="create()" class="shrink-0">
                    <Button class="h-11 rounded-xl bg-[#101D35] px-5 font-semibold text-white hover:bg-[#263F60]">
                        <Plus class="mr-2 h-4 w-4" />
                        Add New Car
                    </Button>
                </Link>
            </section>

            <!-- Summary cards -->
            <section class="grid grid-cols-1 gap-4 sm:grid-cols-2">
                <div class="rounded-2xl border border-[#C5D0DE] bg-white p-5 shadow-sm">
                    <div class="flex items-center justify-between">
                        <p class="text-sm font-medium text-slate-500">Total vehicles</p>
                        <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#E8EDF4] text-[#263F60]">
                            <CarFront class="h-5 w-5" />
                        </span>
                    </div>
                    <p class="mt-3 text-3xl font-bold text-[#101D35]">
                        {{ Object.values(statuses).reduce((total, item) => total + item.count, 0) }}
                    </p>
                    <p class="mt-1 text-xs text-slate-500">Vehicles across all statuses</p>
                </div>

                <div class="rounded-2xl border border-[#C5D0DE] bg-white p-5 shadow-sm">
                    <div class="flex items-center justify-between">
                        <p class="text-sm font-medium text-slate-500">Current results</p>
                        <span class="flex h-10 w-10 items-center justify-center rounded-xl bg-[#E8EDF4] text-[#263F60]">
                            <SlidersHorizontal class="h-5 w-5" />
                        </span>
                    </div>
                    <p class="mt-3 text-3xl font-bold text-[#101D35]">
                        {{ cars.data.length }}
                    </p>
                    <p class="mt-1 text-xs text-slate-500">Vehicles on this page</p>
                </div>
            </section>

            <!-- Search and filters -->
            <section class="rounded-2xl border border-[#C5D0DE] bg-white p-5 shadow-sm sm:p-6">
                <div class="mb-4 flex items-center gap-2">
                    <Search class="h-5 w-5 text-[#58779D]" />
                    <h2 class="font-semibold text-[#101D35]">Find a vehicle</h2>
                </div>

                <div class="flex flex-col gap-3 sm:flex-row">
                    <div class="relative flex-1">
                        <Search class="absolute left-3 top-1/2 h-4 w-4 -translate-y-1/2 text-slate-400" />
                        <Input
                            v-model="search"
                            placeholder="Search make, model or license plate..."
                            class="h-11 rounded-xl border-[#C5D0DE] pl-10 focus-visible:ring-[#58779D]"
                            @keyup.enter="doSearch"
                        />
                    </div>
                    <Button
                        @click="doSearch"
                        class="h-11 rounded-xl bg-[#263F60] px-6 text-white hover:bg-[#101D35]"
                    >
                        Search
                    </Button>
                </div>

                <div class="mt-5">
                    <p class="mb-3 text-xs font-semibold uppercase tracking-wider text-slate-500">
                        Filter by status
                    </p>
                    <div class="flex flex-wrap gap-2">
                        <button
                            type="button"
                            @click="statusFilter = 'all'; doSearch()"
                            class="rounded-xl border px-4 py-2 text-sm font-medium transition"
                            :class="statusFilter === 'all'
                                ? 'border-[#101D35] bg-[#101D35] text-white'
                                : 'border-[#C5D0DE] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                        >
                            All ({{ Object.values(statuses).reduce((total, item) => total + item.count, 0) }})
                        </button>

                        <button
                            v-for="(status, key) in statuses"
                            :key="key"
                            type="button"
                            @click="statusFilter = key; doSearch()"
                            class="inline-flex items-center gap-2 rounded-xl border px-4 py-2 text-sm font-medium transition"
                            :class="statusFilter === key
                                ? 'border-[#101D35] bg-[#101D35] text-white'
                                : 'border-[#C5D0DE] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                        >
                            <span
                                class="h-2 w-2 rounded-full"
                                :style="{ backgroundColor: status.color }"
                            ></span>
                            {{ status.label }} ({{ status.count }})
                        </button>
                    </div>
                </div>
            </section>

            <!-- Vehicle table -->
            <section class="overflow-hidden rounded-2xl border border-[#C5D0DE] bg-white shadow-sm">
                <div class="flex flex-col justify-between gap-2 border-b border-[#E2E8F0] px-5 py-4 sm:flex-row sm:items-center">
                    <div>
                        <h2 class="font-bold text-[#101D35]">Vehicle inventory</h2>
                        <p class="mt-1 text-xs text-slate-500">
                            Showing {{ cars.data.length }} vehicles on this page
                        </p>
                    </div>
                    <span class="inline-flex w-fit items-center gap-2 rounded-lg bg-[#F4F6FA] px-3 py-2 text-xs font-medium text-[#263F60]">
                        <CalendarDays class="h-4 w-4" />
                        Latest vehicles first
                    </span>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-[850px] w-full text-left">
                        <thead class="bg-[#F4F6FA]">
                            <tr>
                                <th class="px-5 py-4 text-xs font-semibold uppercase tracking-wider text-slate-500">Vehicle</th>
                                <th class="px-5 py-4 text-xs font-semibold uppercase tracking-wider text-slate-500">License plate</th>
                                <th class="px-5 py-4 text-xs font-semibold uppercase tracking-wider text-slate-500">Price per day</th>
                                <th class="px-5 py-4 text-xs font-semibold uppercase tracking-wider text-slate-500">Status</th>
                                <th class="px-5 py-4 text-right text-xs font-semibold uppercase tracking-wider text-slate-500">Actions</th>
                            </tr>
                        </thead>

                        <tbody class="divide-y divide-[#E8EDF4]">
                            <tr
                                v-for="car in cars.data"
                                :key="car.id"
                                class="transition hover:bg-[#F8FAFC]"
                            >
                                <td class="px-5 py-4">
                                    <div class="flex items-center gap-3">
                                        <div class="flex h-14 w-20 shrink-0 items-center justify-center overflow-hidden rounded-xl border border-[#E2E8F0] bg-[#F4F6FA]">
                                            <img
                                                v-if="car.image_url"
                                                :src="car.image_url"
                                                :alt="`${car.make} ${car.model}`"
                                                class="h-full w-full object-cover"
                                            />
                                            <CarFront v-else class="h-7 w-7 text-[#58779D]" />
                                        </div>
                                        <div>
                                            <p class="font-semibold text-[#101D35]">
                                                {{ car.make }} {{ car.model }}
                                            </p>
                                            <p class="mt-1 text-xs text-slate-500">{{ car.year }}</p>
                                        </div>
                                    </div>
                                </td>

                                <td class="px-5 py-4">
                                    <span class="rounded-lg bg-[#F4F6FA] px-3 py-2 font-mono text-xs font-medium text-[#263F60]">
                                        {{ car.license_plate }}
                                    </span>
                                </td>

                                <td class="px-5 py-4">
                                    <p class="font-semibold text-[#101D35]">
                                        {{ currency.symbol }}{{ Number(car.price_per_day).toFixed(2) }}
                                    </p>
                                    <p class="mt-1 text-xs text-slate-500">{{ currency.code }} / day</p>
                                </td>

                                <td class="px-5 py-4">
                                    <span
                                        class="inline-flex items-center gap-2 rounded-full px-3 py-1.5 text-xs font-semibold"
                                        :style="{
                                            backgroundColor: getStatusColor(car.status).bg,
                                            color: getStatusColor(car.status).text,
                                        }"
                                    >
                                        <span
                                            class="h-2 w-2 rounded-full"
                                            :style="{ backgroundColor: getStatusColor(car.status).dot }"
                                        ></span>
                                        {{ car.status_label || statuses[car.status]?.label || car.status }}
                                    </span>
                                </td>

                                <td class="px-5 py-4">
                                    <div class="flex items-center justify-end gap-2">
                                        <Link :href="`/admin/cars/${car.id}/edit`">
                                            <Button variant="outline" size="sm" class="rounded-lg border-[#C5D0DE] text-[#263F60] hover:bg-[#E8EDF4]">
                                                <Pencil class="mr-1.5 h-3.5 w-3.5" />
                                                Edit
                                            </Button>
                                        </Link>
                                        <Button
                                            variant="outline"
                                            size="sm"
                                            class="rounded-lg border-red-200 text-red-600 hover:bg-red-50 hover:text-red-700"
                                            @click="openDeleteDialog(car.id)"
                                        >
                                            <Trash2 class="mr-1.5 h-3.5 w-3.5" />
                                            Delete
                                        </Button>
                                    </div>
                                </td>
                            </tr>

                            <tr v-if="cars.data.length === 0">
                                <td colspan="5" class="px-5 py-16 text-center">
                                    <CarFront class="mx-auto h-10 w-10 text-[#9AAAC0]" />
                                    <p class="mt-3 font-semibold text-[#101D35]">No vehicles found</p>
                                    <p class="mt-1 text-sm text-slate-500">Try another search or status filter.</p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <!-- Pagination -->
                <nav
                    v-if="cars.links?.length"
                    aria-label="Pagination"
                    class="flex flex-wrap items-center justify-center gap-2 border-t border-[#E2E8F0] px-4 py-5"
                >
                    <template v-for="(link, i) in cars.links" :key="i">
                        <Link
                            v-if="link.url"
                            :href="link.url"
                            preserve-scroll
                            class="rounded-lg border px-3 py-2 text-sm transition"
                            :class="link.active
                                ? 'border-[#101D35] bg-[#101D35] font-semibold text-white'
                                : 'border-[#C5D0DE] bg-white text-[#263F60] hover:bg-[#F4F6FA]'"
                        >
                            <span v-html="link.label"></span>
                        </Link>
                        <span
                            v-else
                            class="pointer-events-none rounded-lg border border-[#E2E8F0] px-3 py-2 text-sm text-slate-400 opacity-60"
                        >
                            <span v-html="link.label"></span>
                        </span>
                    </template>
                </nav>
            </section>
        </main>

        <!-- Delete confirmation -->
        <Dialog v-model:open="showDeleteDialog">
            <DialogContent class="rounded-2xl sm:max-w-[425px]">
                <DialogHeader>
                    <DialogTitle class="flex items-center gap-2 text-[#101D35]">
                        <AlertCircle class="h-5 w-5 text-red-600" />
                        Delete vehicle?
                    </DialogTitle>
                    <DialogDescription>
                        Are you sure you want to delete this vehicle? This action cannot be undone.
                    </DialogDescription>
                </DialogHeader>

                <Alert variant="destructive" class="mt-2">
                    <AlertCircle class="h-4 w-4" />
                    <AlertDescription>
                        The server may restrict this action in the current demo version.
                    </AlertDescription>
                </Alert>

                <DialogFooter class="mt-4 gap-2">
                    <DialogClose as-child>
                        <Button variant="outline" class="rounded-lg">Cancel</Button>
                    </DialogClose>
                    <Button
                        type="button"
                        variant="destructive"
                        @click="destroyCar"
                        :disabled="!carToDelete"
                        class="rounded-lg"
                    >
                        Confirm delete
                    </Button>
                </DialogFooter>
            </DialogContent>
        </Dialog>
    </AdminLayout>
</template>
```