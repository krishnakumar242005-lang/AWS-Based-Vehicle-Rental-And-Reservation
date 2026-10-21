```vue
<script setup lang="ts">
import InputError from '@/components/InputError.vue';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import AdminLayout from '@/layouts/AdminLayout.vue';
import { Head, Link, useForm } from '@inertiajs/vue3';
import { computed } from 'vue';
import { index, update } from '@/routes/admin/reservations';

const props = defineProps<{
    reservation: any;
    enums: {
        statuses: Array<{ value: string; label: string; color: string }>;
    };
}>();

const statuses = computed(() => props.enums.statuses || []);

const formatDateForInput = (
    dateString: string | null | undefined,
): string => {
    if (!dateString) return '';
    const date = new Date(dateString);
    return date.toISOString().split('T')[0];
};

const form = useForm({
    start_date: formatDateForInput(props.reservation?.start_date) || '',
    end_date: formatDateForInput(props.reservation?.end_date) || '',
    pickup_time: props.reservation?.pickup_time || '09:00',
    return_time: props.reservation?.return_time || '18:00',
    pickup_location: props.reservation?.pickup_location || '',
    return_location: props.reservation?.return_location || '',
    discount_amount: props.reservation?.discount_amount || 0,
    notes: props.reservation?.notes || '',
    status: props.reservation?.status || 'pending',
    cancellation_reason: props.reservation?.cancellation_reason || '',
});

function submit() {
    form.put(update(props.reservation.id).url);
}
</script>

<template>
    <Head :title="`Edit Reservation ${reservation?.reservation_number || ''}`" />

    <AdminLayout>
        <main class="min-h-screen space-y-7 bg-[#F4F6FA] p-4 sm:p-6 lg:p-8">
            <!-- Page Header -->
            <div class="flex flex-col justify-between gap-4 sm:flex-row sm:items-center">
                <div>
                    <p class="text-sm font-semibold uppercase tracking-[0.2em] text-[#58779D]">
                        Reservation Management
                    </p>
                    <h1 class="mt-2 text-2xl font-bold tracking-tight text-[#101D35] sm:text-3xl">
                        Edit Reservation
                    </h1>
                    <p class="mt-1 text-sm text-slate-500">
                        Update booking details, rental dates, and reservation status.
                    </p>
                </div>

                <Link
                    :href="index().url"
                    class="inline-flex w-fit items-center justify-center rounded-xl border border-[#C5D0DE] bg-white px-5 py-2.5 text-sm font-semibold text-[#101D35] transition hover:bg-[#E8EDF4]"
                >
                    ← Back to Reservations
                </Link>
            </div>

            <!-- Reservation Summary -->
            <section class="grid grid-cols-1 gap-4 md:grid-cols-3">
                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center gap-3">
                        <div class="flex h-11 w-11 items-center justify-center rounded-xl bg-[#E8EDF4] text-xl text-[#263F60]">
                            #
                        </div>
                        <div class="min-w-0">
                            <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">
                                Reservation Number
                            </p>
                            <p class="mt-1 break-words font-bold text-[#101D35]">
                                {{ reservation.reservation_number || '—' }}
                            </p>
                        </div>
                    </div>
                </div>

                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center gap-3">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E8EDF4] text-xl">
                            <span class="text-[#263F60]">👤</span>
                        </div>
                        <div class="min-w-0">
                            <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">
                                Client
                            </p>
                            <p class="mt-1 truncate font-bold text-[#101D35]">
                                {{ reservation.user?.name || '—' }}
                            </p>
                            <p class="truncate text-sm text-slate-500">
                                {{ reservation.user?.email || '' }}
                            </p>
                        </div>
                    </div>
                </div>

                <div class="rounded-2xl border border-[#DCE3ED] bg-white p-5 shadow-sm">
                    <div class="flex items-center gap-3">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E8EDF4] text-xl">
                            🚘
                        </div>
                        <div class="min-w-0">
                            <p class="text-xs font-semibold uppercase tracking-wider text-slate-500">
                                Reserved Vehicle
                            </p>
                            <p class="mt-1 break-words font-bold text-[#101D35]">
                                {{
                                    reservation.car
                                        ? `${reservation.car.year} ${reservation.car.make} ${reservation.car.model}`
                                        : '—'
                                }}
                            </p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Edit Form -->
            <form
                class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm"
                @submit.prevent="submit"
            >
                <div class="border-b border-[#DCE3ED] bg-[#101D35] px-5 py-5 sm:px-7">
                    <h2 class="text-lg font-bold text-white">
                        Reservation Details
                    </h2>
                    <p class="mt-1 text-sm text-[#C5D0DE]">
                        Make the necessary changes and save your updates.
                    </p>
                </div>

                <div class="space-y-8 p-5 sm:p-7">
                    <!-- Rental Schedule -->
                    <section>
                        <h3 class="mb-4 flex items-center gap-2 text-base font-bold text-[#101D35]">
                            <span class="h-5 w-1 rounded-full bg-[#58779D]"></span>
                            Rental Schedule
                        </h3>

                        <div class="grid grid-cols-1 gap-5 md:grid-cols-2">
                            <div class="space-y-2">
                                <Label for="start_date" class="font-semibold text-[#263F60]">
                                    Start Date
                                </Label>
                                <Input
                                    id="start_date"
                                    v-model="form.start_date"
                                    type="date"
                                    required
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.start_date" />
                            </div>

                            <div class="space-y-2">
                                <Label for="end_date" class="font-semibold text-[#263F60]">
                                    End Date
                                </Label>
                                <Input
                                    id="end_date"
                                    v-model="form.end_date"
                                    type="date"
                                    required
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.end_date" />
                            </div>

                            <div class="space-y-2">
                                <Label for="pickup_time" class="font-semibold text-[#263F60]">
                                    Pickup Time
                                </Label>
                                <Input
                                    id="pickup_time"
                                    v-model="form.pickup_time"
                                    type="time"
                                    required
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.pickup_time" />
                            </div>

                            <div class="space-y-2">
                                <Label for="return_time" class="font-semibold text-[#263F60]">
                                    Return Time
                                </Label>
                                <Input
                                    id="return_time"
                                    v-model="form.return_time"
                                    type="time"
                                    required
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.return_time" />
                            </div>
                        </div>
                    </section>

                    <div class="border-t border-[#E5EAF1]"></div>

                    <!-- Locations and Pricing -->
                    <section>
                        <h3 class="mb-4 flex items-center gap-2 text-base font-bold text-[#101D35]">
                            <span class="h-5 w-1 rounded-full bg-[#58779D]"></span>
                            Locations & Pricing
                        </h3>

                        <div class="grid grid-cols-1 gap-5 md:grid-cols-2">
                            <div class="space-y-2">
                                <Label for="pickup_location" class="font-semibold text-[#263F60]">
                                    Pickup Location
                                </Label>
                                <Input
                                    id="pickup_location"
                                    v-model="form.pickup_location"
                                    placeholder="Main Office"
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.pickup_location" />
                            </div>

                            <div class="space-y-2">
                                <Label for="return_location" class="font-semibold text-[#263F60]">
                                    Return Location
                                </Label>
                                <Input
                                    id="return_location"
                                    v-model="form.return_location"
                                    placeholder="Main Office"
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.return_location" />
                            </div>

                            <div class="space-y-2">
                                <Label for="discount_amount" class="font-semibold text-[#263F60]">
                                    Discount Amount
                                </Label>
                                <Input
                                    id="discount_amount"
                                    v-model="form.discount_amount"
                                    type="number"
                                    step="0.01"
                                    min="0"
                                    class="rounded-xl border-[#C5D0DE] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="form.errors.discount_amount" />
                            </div>

                            <div class="space-y-2">
                                <Label for="status" class="font-semibold text-[#263F60]">
                                    Reservation Status
                                </Label>
                                <select
                                    id="status"
                                    v-model="form.status"
                                    required
                                    class="block w-full rounded-xl border border-[#C5D0DE] bg-white px-3 py-2.5 text-sm text-[#101D35] outline-none transition focus:border-[#58779D] focus:ring-2 focus:ring-[#58779D]/20"
                                >
                                    <option
                                        v-for="s in statuses"
                                        :key="s.value"
                                        :value="s.value"
                                    >
                                        {{ s.label }}
                                    </option>
                                </select>
                                <InputError :message="form.errors.status" />
                            </div>
                        </div>
                    </section>

                    <div class="border-t border-[#E5EAF1]"></div>

                    <!-- Notes -->
                    <section>
                        <h3 class="mb-4 flex items-center gap-2 text-base font-bold text-[#101D35]">
                            <span class="h-5 w-1 rounded-full bg-[#58779D]"></span>
                            Internal Notes
                        </h3>

                        <div class="space-y-2">
                            <Label for="notes" class="font-semibold text-[#263F60]">
                                Notes
                            </Label>
                            <textarea
                                id="notes"
                                v-model="form.notes"
                                rows="4"
                                placeholder="Add any important information about this reservation..."
                                class="w-full rounded-xl border border-[#C5D0DE] bg-white px-4 py-3 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-2 focus:ring-[#58779D]/20"
                            ></textarea>
                            <InputError :message="form.errors.notes" />
                        </div>
                    </section>

                    <!-- Cancellation Reason -->
                    <section
                        v-if="form.status === 'cancelled'"
                        class="rounded-xl border border-red-200 bg-red-50 p-5"
                    >
                        <h3 class="mb-3 font-bold text-red-800">
                            Cancellation Information
                        </h3>

                        <div class="space-y-2">
                            <Label for="cancellation_reason" class="font-semibold text-red-900">
                                Cancellation Reason
                            </Label>
                            <textarea
                                id="cancellation_reason"
                                v-model="form.cancellation_reason"
                                rows="3"
                                placeholder="Explain why this reservation was cancelled..."
                                class="w-full rounded-xl border border-red-200 bg-white px-4 py-3 text-sm text-slate-800 outline-none focus:border-red-400 focus:ring-2 focus:ring-red-200"
                            ></textarea>
                            <InputError :message="form.errors.cancellation_reason" />
                        </div>
                    </section>
                </div>

                <!-- Form Actions -->
                <div class="flex flex-col-reverse gap-3 border-t border-[#DCE3ED] bg-[#F8FAFC] px-5 py-5 sm:flex-row sm:justify-end sm:px-7">
                    <Link :href="index().url">
                        <Button
                            type="button"
                            variant="outline"
                            class="w-full rounded-xl border-[#C5D0DE] px-6 text-[#263F60] hover:bg-[#E8EDF4] sm:w-auto"
                        >
                            Cancel
                        </Button>
                    </Link>

                    <Button
                        type="submit"
                        :disabled="form.processing"
                        class="w-full rounded-xl bg-[#101D35] px-7 font-semibold text-white hover:bg-[#263F60] disabled:cursor-not-allowed disabled:opacity-60 sm:w-auto"
                    >
                        {{ form.processing ? 'Saving Changes...' : 'Save Changes' }}
                    </Button>
                </div>
            </form>
        </main>
    </AdminLayout>
</template>
```