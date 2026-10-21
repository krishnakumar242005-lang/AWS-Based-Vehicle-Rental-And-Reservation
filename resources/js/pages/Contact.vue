
<script setup lang="ts">
import HomeLayout from '@/layouts/HomeLayout.vue';
import { useForm } from '@inertiajs/vue3';
import { guestContact } from '@/routes/contact';
import { ref, onBeforeUnmount } from 'vue';
import { fleet, about } from '@/routes';

const form = useForm({
    name: '',
    email: '',
    subject: '',
    message: '',
});

const showNotification = ref(false);
const notificationMessage = ref('');
const notificationType = ref<'success' | 'error'>('success');

let notificationTimer: ReturnType<typeof setTimeout> | undefined;

const notify = (message: string, type: 'success' | 'error') => {
    notificationMessage.value = message;
    notificationType.value = type;
    showNotification.value = true;

    if (notificationTimer) clearTimeout(notificationTimer);

    notificationTimer = setTimeout(() => {
        showNotification.value = false;
    }, 3000);
};

onBeforeUnmount(() => {
    if (notificationTimer) clearTimeout(notificationTimer);
});

const sendTicket = () => {
    form.post(guestContact().url, {
        onSuccess() {
            form.reset();
            notify('Message sent successfully!', 'success');
        },
        onError() {
            notify('Unable to send your message. Please check the fields and try again.', 'error');
        },
    });
};
</script>

<template>
    <HomeLayout>
        <Head title="Contact Us" />

        <main class="min-h-screen bg-[#F4F6FA]">
            <!-- Notification -->
            <Transition
                enter-active-class="transition duration-300 ease-out"
                enter-from-class="translate-y-[-10px] opacity-0"
                enter-to-class="translate-y-0 opacity-100"
                leave-active-class="transition duration-200 ease-in"
                leave-from-class="opacity-100"
                leave-to-class="opacity-0"
            >
                <div
                    v-if="showNotification"
                    role="status"
                    aria-live="polite"
                    :class="[
                        'fixed right-4 top-24 z-50 max-w-sm rounded-xl px-5 py-4 text-sm font-semibold text-white shadow-xl',
                        notificationType === 'success'
                            ? 'bg-emerald-700'
                            : 'bg-rose-700',
                    ]"
                >
                    {{ notificationMessage }}
                </div>
            </Transition>

            <!-- Hero -->
            <section class="relative overflow-hidden bg-gradient-to-br from-[#101D35] via-[#1B3150] to-[#263F60] px-4 py-16 text-white sm:py-20">
                <div class="pointer-events-none absolute -right-16 -top-24 h-72 w-72 rounded-full border border-white/10"></div>
                <div class="pointer-events-none absolute -right-4 -top-12 h-52 w-52 rounded-full border border-white/10"></div>

                <div class="relative mx-auto max-w-4xl text-center">
                    <span class="inline-flex items-center gap-2 rounded-full border border-white/20 bg-white/10 px-4 py-2 text-xs font-semibold tracking-[0.18em] text-[#C5D0DE] uppercase">
                        <span class="h-2 w-2 rounded-full bg-[#C5D0DE]"></span>
                        We're here to help
                    </span>

                    <h1 class="mt-6 text-4xl font-bold tracking-tight sm:text-5xl lg:text-6xl">
                        Let's Talk
                    </h1>

                    <p class="mx-auto mt-5 max-w-2xl text-base leading-7 text-slate-300 sm:text-lg">
                        Have a question about a vehicle, your reservation, or our rental service?
                        Send us a message and our team can help.
                    </p>
                </div>
            </section>

            <!-- Main content -->
            <section class="mx-auto max-w-7xl px-4 py-12 sm:px-6 sm:py-16 lg:px-8">
                <div class="grid items-start gap-8 lg:grid-cols-5 lg:gap-10">
                    <!-- Contact form -->
                    <div class="lg:col-span-3">
                        <div class="overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm">
                            <div class="border-b border-[#E5EAF1] px-6 py-6 sm:px-8">
                                <p class="text-xs font-semibold tracking-[0.18em] text-[#58779D] uppercase">
                                    Contact form
                                </p>
                                <h2 class="mt-2 text-2xl font-bold text-[#101D35] sm:text-3xl">
                                    Send us a message
                                </h2>
                                <p class="mt-2 text-sm leading-6 text-slate-500">
                                    Fill in the details below so we can understand how to help you.
                                </p>
                            </div>

                            <form class="space-y-6 p-6 sm:p-8" @submit.prevent="sendTicket">
                                <div class="grid gap-6 sm:grid-cols-2">
                                    <!-- Name -->
                                    <div>
                                        <label for="name" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                            Full name <span class="text-rose-600">*</span>
                                        </label>
                                        <input
                                            id="name"
                                            v-model="form.name"
                                            type="text"
                                            name="name"
                                            autocomplete="name"
                                            required
                                            placeholder="Enter your full name"
                                            class="w-full rounded-xl border border-[#DCE3ED] bg-white px-4 py-3 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-4 focus:ring-[#58779D]/10"
                                            :aria-invalid="!!form.errors.name"
                                        />
                                        <p v-if="form.errors.name" class="mt-2 text-sm text-rose-600">
                                            {{ form.errors.name }}
                                        </p>
                                    </div>

                                    <!-- Email -->
                                    <div>
                                        <label for="email" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                            Email address <span class="text-rose-600">*</span>
                                        </label>
                                        <input
                                            id="email"
                                            v-model="form.email"
                                            type="email"
                                            name="email"
                                            autocomplete="email"
                                            required
                                            placeholder="you@example.com"
                                            class="w-full rounded-xl border border-[#DCE3ED] bg-white px-4 py-3 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-4 focus:ring-[#58779D]/10"
                                            :aria-invalid="!!form.errors.email"
                                        />
                                        <p v-if="form.errors.email" class="mt-2 text-sm text-rose-600">
                                            {{ form.errors.email }}
                                        </p>
                                    </div>
                                </div>

                                <!-- Subject -->
                                <div>
                                    <label for="subject" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                        Subject <span class="text-rose-600">*</span>
                                    </label>
                                    <input
                                        id="subject"
                                        v-model="form.subject"
                                        type="text"
                                        name="subject"
                                        required
                                        placeholder="What would you like help with?"
                                        class="w-full rounded-xl border border-[#DCE3ED] bg-white px-4 py-3 text-sm text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-4 focus:ring-[#58779D]/10"
                                        :aria-invalid="!!form.errors.subject"
                                    />
                                    <p v-if="form.errors.subject" class="mt-2 text-sm text-rose-600">
                                        {{ form.errors.subject }}
                                    </p>
                                </div>

                                <!-- Message -->
                                <div>
                                    <label for="message" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                        Your message <span class="text-rose-600">*</span>
                                    </label>
                                    <textarea
                                        id="message"
                                        v-model="form.message"
                                        name="message"
                                        rows="6"
                                        required
                                        placeholder="Tell us how we can help..."
                                        class="w-full resize-y rounded-xl border border-[#DCE3ED] bg-white px-4 py-3 text-sm leading-6 text-[#101D35] outline-none transition placeholder:text-slate-400 focus:border-[#58779D] focus:ring-4 focus:ring-[#58779D]/10"
                                        :aria-invalid="!!form.errors.message"
                                    ></textarea>
                                    <p v-if="form.errors.message" class="mt-2 text-sm text-rose-600">
                                        {{ form.errors.message }}
                                    </p>
                                </div>

                                <!-- Submit -->
                                <button
                                    type="submit"
                                    :disabled="form.processing"
                                    class="flex w-full items-center justify-center gap-3 rounded-xl bg-[#101D35] px-6 py-4 text-sm font-bold text-white shadow-sm transition hover:bg-[#263F60] focus:outline-none focus:ring-4 focus:ring-[#58779D]/25 disabled:cursor-not-allowed disabled:opacity-60"
                                >
                                    <svg
                                        v-if="!form.processing"
                                        xmlns="http://www.w3.org/2000/svg"
                                        class="h-5 w-5"
                                        fill="none"
                                        viewBox="0 0 24 24"
                                        stroke="currentColor"
                                        stroke-width="1.8"
                                    >
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M21 3L10 14m11-11l-7 18-4-7-7-4 18-7z" />
                                    </svg>

                                    <svg
                                        v-else
                                        class="h-5 w-5 animate-spin"
                                        xmlns="http://www.w3.org/2000/svg"
                                        fill="none"
                                        viewBox="0 0 24 24"
                                    >
                                        <circle class="opacity-25" cx="12" cy="12" r="10" stroke="currentColor" stroke-width="4" />
                                        <path class="opacity-75" fill="currentColor" d="M4 12a8 8 0 018-8V0C5.373 0 0 5.373 0 12h4z" />
                                    </svg>

                                    {{ form.processing ? 'Sending message...' : 'Send Message' }}
                                </button>

                                <p class="text-center text-xs text-slate-500">
                                    Fields marked with <span class="text-rose-600">*</span> are required.
                                </p>
                            </form>
                        </div>
                    </div>

                    <!-- Sidebar -->
                    <aside class="space-y-6 lg:col-span-2">
                        <div class="rounded-2xl bg-[#101D35] p-6 text-white shadow-sm sm:p-8">
                            <div class="flex h-12 w-12 items-center justify-center rounded-xl bg-white/10 text-[#C5D0DE]">
                                <svg xmlns="http://www.w3.org/2000/svg" class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor" stroke-width="1.7">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M8 10h.01M12 10h.01M16 10h.01M9 16H5a2 2 0 01-2-2V6a2 2 0 012-2h14a2 2 0 012 2v8a2 2 0 01-2 2h-5l-4 4v-4z" />
                                </svg>
                            </div>

                            <h2 class="mt-5 text-2xl font-bold">How can we help?</h2>
                            <p class="mt-3 text-sm leading-6 text-slate-300">
                                Whether you're planning a trip or have a question about an existing booking,
                                send us the details using the form.
                            </p>

                            <div class="mt-6 space-y-4 border-t border-white/15 pt-6">
                                <div class="flex items-start gap-3">
                                    <span class="mt-0.5 text-[#C5D0DE]">01</span>
                                    <div>
                                        <p class="font-semibold">Vehicle enquiries</p>
                                        <p class="mt-1 text-sm text-slate-300">Ask about the vehicles available in our fleet.</p>
                                    </div>
                                </div>

                                <div class="flex items-start gap-3">
                                    <span class="mt-0.5 text-[#C5D0DE]">02</span>
                                    <div>
                                        <p class="font-semibold">Reservation support</p>
                                        <p class="mt-1 text-sm text-slate-300">Get help with booking-related questions.</p>
                                    </div>
                                </div>

                                <div class="flex items-start gap-3">
                                    <span class="mt-0.5 text-[#C5D0DE]">03</span>
                                    <div>
                                        <p class="font-semibold">General enquiries</p>
                                        <p class="mt-1 text-sm text-slate-300">Share feedback or ask us a question.</p>
                                    </div>
                                </div>
                            </div>
                        </div>

                        <!-- Quick links -->
                        <div class="rounded-2xl border border-[#DCE3ED] bg-white p-6 shadow-sm sm:p-7">
                            <h3 class="text-lg font-bold text-[#101D35]">Explore Real Rent Car</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Continue exploring our website while we're here to help.
                            </p>

                            <div class="mt-5 space-y-3">
                                <a
                                    :href="fleet.url()"
                                    class="group flex items-center justify-between rounded-xl border border-[#DCE3ED] p-4 transition hover:border-[#58779D] hover:bg-[#F4F6FA]"
                                >
                                    <div>
                                        <p class="font-semibold text-[#263F60]">Browse our fleet</p>
                                        <p class="mt-1 text-xs text-slate-500">Explore available vehicles</p>
                                    </div>
                                    <span class="text-lg text-[#58779D] transition group-hover:translate-x-1">→</span>
                                </a>

                                <a
                                    :href="about.url()"
                                    class="group flex items-center justify-between rounded-xl border border-[#DCE3ED] p-4 transition hover:border-[#58779D] hover:bg-[#F4F6FA]"
                                >
                                    <div>
                                        <p class="font-semibold text-[#263F60]">About us</p>
                                        <p class="mt-1 text-xs text-slate-500">Learn about our service</p>
                                    </div>
                                    <span class="text-lg text-[#58779D] transition group-hover:translate-x-1">→</span>
                                </a>
                            </div>
                        </div>
                    </aside>
                </div>
            </section>
        </main>
    </HomeLayout>
</template>
