```vue
<script setup lang="ts">
import AdminLayout from '@/layouts/AdminLayout.vue';
import { router, usePage } from '@inertiajs/vue3';
import { Chart, registerables } from 'chart.js';
import { computed, onMounted, onBeforeUnmount, ref, watch } from 'vue';

Chart.register(...registerables);

interface KPI {
    value: number;
    formatted: string;
    label: string;
}

interface CarState {
    value: number;
    formatted: string;
    label: string;
    color: string;
}

interface ChartData {
    labels: string[];
    datasets: Array<{
        label: string;
        data: number[];
        backgroundColor: string;
        borderColor: string;
        borderWidth: number;
    }>;
    dailyTotals: number[];
    statusColors: Record<string, string>;
    statusLabels: Record<string, string>;
    dateRange: { start: string; end: string };
}

interface CarPerformance {
    id: number;
    car_name: string;
    license_plate: string;
    status: string;
    status_color: string;
    total_reservations: number;
    total_revenue: number;
    formatted_revenue: string;
    total_days: number;
    utilization_rate: number;
    average_per_reservation: number;
}

interface PeriodOption {
    value: string;
    label: string;
}

interface PageProps {
    kpis: {
        totalRevenue: KPI;
        platformVisits: KPI;
        activeReservations: KPI;
        newClients: KPI;
    };
    carsState: {
        totalCars: CarState;
        availableCars: CarState;
        rentedCars: CarState;
        unavailableCars: CarState;
    };
    reservationsChart: ChartData;
    carsPerformance: CarPerformance[];
    currentPeriod: string;
    periodOptions: PeriodOption[];
}

const page = usePage<PageProps>();
const selectedPeriod = ref(page.props.currentPeriod);
const reservationChart = ref<Chart | null>(null);
const chartCanvas = ref<HTMLCanvasElement | null>(null);

const sortField = ref<keyof CarPerformance>('total_revenue');
const sortDirection = ref<'asc' | 'desc'>('desc');

const kpis = computed(() => page.props.kpis);
const carsState = computed(() => page.props.carsState);
const chartData = computed(() => page.props.reservationsChart);
const periodOptions = computed(() => page.props.periodOptions);

const sortedCarsPerformance = computed(() => {
    const sorted = [...(page.props.carsPerformance ?? [])].sort((a, b) => {
        const aValue = a[sortField.value];
        const bValue = b[sortField.value];

        if (typeof aValue === 'string' && typeof bValue === 'string') {
            return sortDirection.value === 'asc'
                ? aValue.localeCompare(bValue)
                : bValue.localeCompare(aValue);
        }

        const numA = Number(aValue);
        const numB = Number(bValue);

        return sortDirection.value === 'asc'
            ? numA - numB
            : numB - numA;
    });

    return sorted.slice(0, 10);
});

const handlePeriodChange = () => {
    router.get(
        '/admin/reports',
        { period: selectedPeriod.value },
        {
            preserveState: false,
            preserveScroll: false,
            only: [
                'kpis',
                'carsState',
                'reservationsChart',
                'carsPerformance',
                'currentPeriod',
            ],
        },
    );
};

const sortTable = (field: keyof CarPerformance) => {
    if (sortField.value === field) {
        sortDirection.value =
            sortDirection.value === 'asc' ? 'desc' : 'asc';
    } else {
        sortField.value = field;
        sortDirection.value = 'desc';
    }
};

const createReservationsChart = () => {
    if (!chartCanvas.value || !chartData.value) return;

    reservationChart.value?.destroy();

    const ctx = chartCanvas.value.getContext('2d');
    if (!ctx) return;

    reservationChart.value = new Chart(ctx, {
        type: 'bar',
        data: {
            labels: chartData.value.labels,
            datasets: chartData.value.datasets.map((dataset) => ({
                ...dataset,
                borderRadius: 4,
                borderSkipped: false,
            })),
        },
        options: {
            responsive: true,
            maintainAspectRatio: false,
            plugins: {
                legend: {
                    display: true,
                    position: 'top',
                    labels: {
                        color: '#263F60',
                        padding: 20,
                        usePointStyle: true,
                        pointStyle: 'rectRounded',
                    },
                },
                tooltip: {
                    mode: 'index',
                    intersect: false,
                    backgroundColor: '#101D35',
                    titleColor: '#FFFFFF',
                    bodyColor: '#E2E8F0',
                    borderColor: '#58779D',
                    borderWidth: 1,
                    callbacks: {
                        title: (items) => `Date: ${items[0].label}`,
                        afterBody: (items) => {
                            const total =
                                chartData.value.dailyTotals[
                                    items[0].dataIndex
                                ];
                            return ['', `Total Reservations: ${total}`];
                        },
                        label: (context) =>
                            `${context.dataset.label || ''}: ${context.parsed.y}`,
                    },
                },
            },
            scales: {
                x: {
                    stacked: true,
                    grid: { display: false },
                    ticks: {
                        color: '#58779D',
                        maxRotation: 45,
                        minRotation: 0,
                    },
                },
                y: {
                    stacked: true,
                    beginAtZero: true,
                    ticks: {
                        color: '#58779D',
                        stepSize: 1,
                        callback: (value) =>
                            Number.isInteger(Number(value)) ? value : '',
                    },
                    title: {
                        display: true,
                        text: 'Number of Reservations',
                        color: '#263F60',
                        font: { size: 12, weight: 'bold' },
                    },
                    grid: { color: 'rgba(88, 119, 157, 0.12)' },
                },
            },
            interaction: { intersect: false, mode: 'index' },
            animation: { duration: 700 },
        },
    });
};

watch(
    () => [chartData.value, selectedPeriod.value],
    createReservationsChart,
    { deep: true },
);

watch(
    () => page.props.currentPeriod,
    (newPeriod) => {
        selectedPeriod.value = newPeriod;
    },
);

onMounted(createReservationsChart);

onBeforeUnmount(() => {
    reservationChart.value?.destroy();
    reservationChart.value = null;
});
</script>

<template>
    <AdminLayout>
        <div class="min-h-full space-y-7 bg-[#F4F6FA] px-4 py-6 sm:px-6 lg:px-8">
            <!-- Page heading -->
            <section class="flex flex-col gap-4 rounded-2xl bg-[#101D35] p-6 text-white shadow-lg sm:flex-row sm:items-center sm:justify-between">
                <div>
                    <p class="mb-2 text-xs font-semibold uppercase tracking-[0.25em] text-[#C5D0DE]">
                        Business overview
                    </p>
                    <h1 class="text-2xl font-bold sm:text-3xl">Reports & Analytics</h1>
                    <p class="mt-2 text-sm text-slate-300">
                        Track revenue, reservations and vehicle performance.
                    </p>
                </div>

                <div class="flex flex-col gap-2 sm:min-w-48">
                    <label for="period" class="text-xs font-semibold uppercase tracking-wider text-[#C5D0DE]">
                        Reporting period
                    </label>
                    <select
                        id="period"
                        v-model="selectedPeriod"
                        @change="handlePeriodChange"
                        class="rounded-xl border border-[#58779D] bg-[#263F60] px-4 py-3 text-sm font-medium text-white outline-none focus:ring-2 focus:ring-[#C5D0DE]"
                    >
                        <option v-for="option in periodOptions" :key="option.value" :value="option.value">
                            {{ option.label }}
                        </option>
                    </select>
                </div>
            </section>

            <!-- Main KPIs -->
            <section>
                <div class="mb-4">
                    <h2 class="text-lg font-bold text-[#101D35]">Business summary</h2>
                    <p class="text-sm text-slate-500">Key figures for the selected period</p>
                </div>

                <div class="grid grid-cols-1 gap-4 sm:grid-cols-2 xl:grid-cols-4">
                    <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm transition hover:-translate-y-1 hover:shadow-md">
                        <div class="flex items-start justify-between">
                            <div>
                                <p class="text-sm font-medium text-slate-500">{{ kpis.totalRevenue.label }}</p>
                                <p class="mt-3 break-words text-2xl font-bold text-[#101D35]">{{ kpis.totalRevenue.formatted }}</p>
                            </div>
                            <span class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M12 8c-1.7 0-3 .9-3 2s1.3 2 3 2 3 .9 3 2-1.3 2-3 2m0-8c1.1 0 2.1.4 2.6 1M12 8V6m0 12v-2m8-4a8 8 0 1 1-16 0 8 8 0 0 1 16 0Z"/>
                                </svg>
                            </span>
                        </div>
                        <div class="mt-5 h-1 rounded-full bg-[#C5D0DE]"><div class="h-1 w-3/4 rounded-full bg-[#263F60]"></div></div>
                    </article>

                    <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm transition hover:-translate-y-1 hover:shadow-md">
                        <div class="flex items-start justify-between">
                            <div>
                                <p class="text-sm font-medium text-slate-500">{{ kpis.platformVisits.label }}</p>
                                <p class="mt-3 text-2xl font-bold text-[#101D35]">{{ kpis.platformVisits.formatted }}</p>
                            </div>
                            <span class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M2.5 12s3.4-7 9.5-7 9.5 7 9.5 7-3.4 7-9.5 7-9.5-7-9.5-7Z"/><circle cx="12" cy="12" r="3" stroke-width="1.8"/>
                                </svg>
                            </span>
                        </div>
                        <div class="mt-5 h-1 rounded-full bg-[#C5D0DE]"><div class="h-1 w-2/3 rounded-full bg-[#58779D]"></div></div>
                    </article>

                    <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm transition hover:-translate-y-1 hover:shadow-md">
                        <div class="flex items-start justify-between">
                            <div>
                                <p class="text-sm font-medium text-slate-500">{{ kpis.activeReservations.label }}</p>
                                <p class="mt-3 text-2xl font-bold text-[#101D35]">{{ kpis.activeReservations.formatted }}</p>
                            </div>
                            <span class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <rect x="3" y="5" width="18" height="16" rx="2" stroke-width="1.8"/><path stroke-linecap="round" stroke-width="1.8" d="M16 3v4M8 3v4M3 10h18"/>
                                </svg>
                            </span>
                        </div>
                        <div class="mt-5 h-1 rounded-full bg-[#C5D0DE]"><div class="h-1 w-1/2 rounded-full bg-[#263F60]"></div></div>
                    </article>

                    <article class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm transition hover:-translate-y-1 hover:shadow-md">
                        <div class="flex items-start justify-between">
                            <div>
                                <p class="text-sm font-medium text-slate-500">{{ kpis.newClients.label }}</p>
                                <p class="mt-3 text-2xl font-bold text-[#101D35]">{{ kpis.newClients.formatted }}</p>
                            </div>
                            <span class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                                <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M16 21v-2a4 4 0 0 0-4-4H8a4 4 0 0 0-4 4v2m6-10a4 4 0 1 0 0-8 4 4 0 0 0 0 8Zm8-7.5a4 4 0 0 1 0 7.8M20 14a4 4 0 0 1 2 3.5V21"/>
                                </svg>
                            </span>
                        </div>
                        <div class="mt-5 h-1 rounded-full bg-[#C5D0DE]"><div class="h-1 w-2/3 rounded-full bg-[#58779D]"></div></div>
                    </article>
                </div>
            </section>

            <!-- Fleet status -->
            <section>
                <div class="mb-4">
                    <h2 class="text-lg font-bold text-[#101D35]">Fleet overview</h2>
                    <p class="text-sm text-slate-500">Current vehicle availability</p>
                </div>

                <div class="grid grid-cols-1 gap-4 sm:grid-cols-2 xl:grid-cols-4">
                    <article v-for="(car, key) in carsState" :key="key" class="flex items-center gap-4 rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                        <span class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl bg-[#E5EBF3] text-[#263F60]">
                            <svg class="h-6 w-6" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="1.8" d="M3 13l2-6h14l2 6v6h-3v-2H6v2H3v-6Zm2 0h14M7 10h.01M17 10h.01"/>
                            </svg>
                        </span>
                        <div class="min-w-0">
                            <p class="text-sm font-medium text-slate-500">{{ car.label }}</p>
                            <p class="mt-1 text-2xl font-bold text-[#101D35]">{{ car.formatted }}</p>
                            <span class="mt-2 inline-flex rounded-full bg-[#EEF2F7] px-2.5 py-1 text-xs font-semibold text-[#263F60]">
                                Fleet status
                            </span>
                        </div>
                    </article>
                </div>
            </section>

            <!-- Reservations chart -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex flex-col gap-2 border-b border-[#E5EAF1] px-5 py-5 sm:flex-row sm:items-center sm:justify-between sm:px-6">
                    <div>
                        <h2 class="text-lg font-bold text-[#101D35]">Daily reservations</h2>
                        <p class="mt-1 text-sm text-slate-500">Reservation activity over time</p>
                    </div>
                    <span class="inline-flex w-fit rounded-lg bg-[#EEF2F7] px-3 py-2 text-xs font-semibold text-[#263F60]">
                        {{ chartData.dateRange.start }} – {{ chartData.dateRange.end }}
                    </span>
                </div>
                <div class="p-4 sm:p-6">
                    <div class="relative h-72 sm:h-96">
                        <canvas ref="chartCanvas"></canvas>
                    </div>
                </div>
            </section>

            <!-- Car performance -->
            <section class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                <div class="flex flex-col gap-2 border-b border-[#E5EAF1] px-5 py-5 sm:flex-row sm:items-center sm:justify-between sm:px-6">
                    <div>
                        <h2 class="text-lg font-bold text-[#101D35]">Top 10 car performance</h2>
                        <p class="mt-1 text-sm text-slate-500">Compare reservations, revenue and vehicle utilization.</p>
                    </div>
                    <span class="w-fit rounded-lg bg-[#EEF2F7] px-3 py-2 text-xs font-medium text-[#263F60]">
                        Click a column to sort
                    </span>
                </div>

                <div class="overflow-x-auto">
                    <table class="min-w-[900px] w-full text-left">
                        <thead class="bg-[#101D35] text-xs font-semibold uppercase tracking-wider text-[#C5D0DE]">
                            <tr>
                                <th v-for="column in [
                                    { key: 'car_name', label: 'Vehicle' },
                                    { key: 'status', label: 'Status' },
                                    { key: 'total_reservations', label: 'Reservations' },
                                    { key: 'total_revenue', label: 'Revenue' },
                                    { key: 'utilization_rate', label: 'Utilization' },
                                    { key: 'average_per_reservation', label: 'Avg. per reservation' },
                                ]" :key="column.key" class="px-5 py-4">
                                    <button type="button" class="flex items-center gap-2 text-left hover:text-white" @click="sortTable(column.key as keyof CarPerformance)">
                                        {{ column.label }}
                                        <span v-if="sortField === column.key" class="text-[#C5D0DE]">
                                            {{ sortDirection === 'asc' ? '↑' : '↓' }}
                                        </span>
                                    </button>
                                </th>
                            </tr>
                        </thead>

                        <tbody class="divide-y divide-[#E5EAF1]">
                            <tr v-for="car in sortedCarsPerformance" :key="car.id" class="transition hover:bg-[#F4F6FA]">
                                <td class="px-5 py-4">
                                    <p class="font-semibold text-[#101D35]">{{ car.car_name }}</p>
                                    <p class="mt-1 text-xs text-slate-500">{{ car.license_plate }}</p>
                                </td>
                                <td class="px-5 py-4">
                                    <span class="inline-flex rounded-full px-3 py-1 text-xs font-semibold" :style="{ backgroundColor: car.status_color, color: '#FFFFFF' }">
                                        {{ car.status }}
                                    </span>
                                </td>
                                <td class="px-5 py-4 font-semibold text-[#263F60]">{{ car.total_reservations }}</td>
                                <td class="px-5 py-4 font-bold text-[#101D35]">{{ car.formatted_revenue }}</td>
                                <td class="px-5 py-4">
                                    <div class="flex min-w-32 items-center gap-3">
                                        <div class="h-2 w-24 overflow-hidden rounded-full bg-[#E5EAF1]">
                                            <div class="h-full rounded-full bg-[#263F60]" :style="{ width: `${Math.min(Math.max(car.utilization_rate, 0), 100)}%` }"></div>
                                        </div>
                                        <span class="text-sm font-semibold text-[#263F60]">{{ car.utilization_rate }}%</span>
                                    </div>
                                </td>
                                <td class="px-5 py-4 font-medium text-[#263F60]">
                                    {{ car.average_per_reservation > 0 ? `$${car.average_per_reservation.toFixed(2)}` : '$0.00' }}
                                </td>
                            </tr>

                            <tr v-if="sortedCarsPerformance.length === 0">
                                <td colspan="6" class="px-5 py-12 text-center">
                                    <p class="font-semibold text-[#101D35]">No vehicle performance data</p>
                                    <p class="mt-1 text-sm text-slate-500">Try selecting a different reporting period.</p>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                </div>

                <div class="border-t border-[#E5EAF1] bg-[#F8FAFC] px-5 py-3 text-xs text-slate-500">
                    Showing up to 10 vehicles · Select a column heading to change the sort order.
                </div>
            </section>
        </div>
    </AdminLayout>
</template>
```