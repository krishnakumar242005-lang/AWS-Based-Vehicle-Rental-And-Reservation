```vue
<script setup lang="ts">
import AuthenticatedSessionController from '@/actions/App/Http/Controllers/Auth/AuthenticatedSessionController';
import InputError from '@/components/InputError.vue';
import TextLink from '@/components/TextLink.vue';
import { Button } from '@/components/ui/button';
import { Checkbox } from '@/components/ui/checkbox';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import HomeLayout from '@/layouts/HomeLayout.vue';
import { register } from '@/routes';
import { request } from '@/routes/password';
import { Form, Head } from '@inertiajs/vue3';
import {
    ChevronDown,
    ChevronUp,
    LoaderCircle,
    Shield,
    User,
    CarFront,
    LockKeyhole,
} from 'lucide-vue-next';
import { ref } from 'vue';

defineProps<{
    status?: string;
    canResetPassword: boolean;
}>();

const isDemoOpen = ref(false);
</script>

<template>
    <HomeLayout>
        <Head title="Log in" />

        <main class="relative min-h-[90vh] overflow-hidden bg-[#F4F6FA] px-4 py-12 sm:px-6 lg:px-8">
            <!-- Background decoration -->
            <div class="pointer-events-none absolute -left-24 top-10 h-72 w-72 rounded-full bg-[#C5D0DE]/40 blur-3xl"></div>
            <div class="pointer-events-none absolute -right-24 bottom-10 h-80 w-80 rounded-full bg-[#58779D]/10 blur-3xl"></div>

            <div class="relative mx-auto grid w-full max-w-5xl overflow-hidden rounded-3xl border border-[#C5D0DE]/60 bg-white shadow-xl lg:grid-cols-2">

                <!-- Left Brand Panel -->
                <section class="relative hidden overflow-hidden bg-[#101D35] p-10 text-white lg:flex lg:flex-col lg:justify-between xl:p-12">
                    <div class="absolute -right-20 top-24 h-64 w-64 rounded-full border border-white/10"></div>
                    <div class="absolute -right-8 top-36 h-40 w-40 rounded-full border border-white/10"></div>
                    <div class="absolute -bottom-24 -left-20 h-72 w-72 rounded-full bg-[#263F60]/70 blur-2xl"></div>

                    <div class="relative">
                        <div class="mb-12 flex h-14 w-14 items-center justify-center rounded-2xl bg-[#C5D0DE] text-[#101D35] shadow-lg">
                            <CarFront class="h-8 w-8" />
                        </div>

                        <p class="mb-4 text-xs font-bold uppercase tracking-[0.25em] text-[#C5D0DE]">
                            WELCOME TO REAL RENT CAR
                        </p>

                        <h1 class="text-4xl font-extrabold leading-tight xl:text-5xl">
                            Your next journey
                            <span class="mt-2 block text-[#C5D0DE]">starts here.</span>
                        </h1>

                        <p class="mt-6 max-w-sm leading-7 text-slate-300">
                            Sign in to explore vehicles, manage your
                            reservations and keep your travel plans organised.
                        </p>
                    </div>

                    <div class="relative mt-12 space-y-5">
                        <div class="flex items-center gap-4 rounded-xl border border-white/10 bg-white/5 p-4">
                            <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-lg bg-white/10 text-[#C5D0DE]">
                                <CarFront class="h-6 w-6" />
                            </div>
                            <div>
                                <h3 class="font-semibold">Explore your options</h3>
                                <p class="mt-1 text-sm text-slate-400">Find a vehicle for your journey.</p>
                            </div>
                        </div>

                        <div class="flex items-center gap-4 rounded-xl border border-white/10 bg-white/5 p-4">
                            <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-lg bg-white/10 text-[#C5D0DE]">
                                <LockKeyhole class="h-6 w-6" />
                            </div>
                            <div>
                                <h3 class="font-semibold">Access your account</h3>
                                <p class="mt-1 text-sm text-slate-400">View the features available to your role.</p>
                            </div>
                        </div>

                        <p class="pt-3 text-xs text-slate-500">
                            A simpler way to plan your next drive.
                        </p>
                    </div>
                </section>

                <!-- Right Login Panel -->
                <section class="p-6 sm:p-10 xl:p-12">
                    <!-- Mobile brand -->
                    <div class="mb-8 flex items-center gap-3 lg:hidden">
                        <div class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#101D35] text-[#C5D0DE]">
                            <CarFront class="h-7 w-7" />
                        </div>
                        <div>
                            <p class="font-extrabold text-[#101D35]">Real Rent Car</p>
                            <p class="text-xs text-slate-500">Your journey starts here</p>
                        </div>
                    </div>

                    <div class="mb-8">
                        <p class="mb-2 text-sm font-bold uppercase tracking-[0.18em] text-[#58779D]">
                            YOUR ACCOUNT
                        </p>
                        <h2 class="text-3xl font-extrabold tracking-tight text-[#101D35] sm:text-4xl">
                            Welcome back
                        </h2>
                        <p class="mt-3 leading-6 text-slate-500">
                            Sign in to continue to your account.
                        </p>
                    </div>

                    <!-- Demo Credentials -->
                    <div class="mb-6 overflow-hidden rounded-xl border border-[#C5D0DE] bg-[#F4F6FA]">
                        <button
                            type="button"
                            @click="isDemoOpen = !isDemoOpen"
                            class="flex w-full items-center justify-between gap-3 px-4 py-4 text-left transition hover:bg-[#E9EEF5]"
                            :aria-expanded="isDemoOpen"
                        >
                            <div class="flex items-center gap-3">
                                <div class="flex h-10 w-10 items-center justify-center rounded-lg bg-[#101D35] text-[#C5D0DE]">
                                    <Shield class="h-5 w-5" />
                                </div>
                                <div>
                                    <p class="font-bold text-[#101D35]">Demo credentials</p>
                                    <p class="mt-1 text-xs text-slate-500">View the sample login details</p>
                                </div>
                            </div>

                            <ChevronUp v-if="isDemoOpen" class="h-5 w-5 text-[#263F60]" />
                            <ChevronDown v-else class="h-5 w-5 text-[#263F60]" />
                        </button>

                        <div v-if="isDemoOpen" class="space-y-3 border-t border-[#C5D0DE] p-4">
                            <div class="rounded-lg border border-[#C5D0DE]/70 bg-white p-4">
                                <div class="mb-3 flex items-center gap-2">
                                    <User class="h-4 w-4 text-[#58779D]" />
                                    <span class="text-xs font-bold uppercase tracking-wider text-[#263F60]">
                                        Client access
                                    </span>
                                </div>
                                <div class="space-y-2 text-sm">
                                    <div class="flex flex-wrap items-center justify-between gap-2">
                                        <span class="text-slate-500">Email</span>
                                        <code class="break-all rounded bg-[#F4F6FA] px-2 py-1 text-[#101D35]">client@example.com</code>
                                    </div>
                                    <div class="flex flex-wrap items-center justify-between gap-2">
                                        <span class="text-slate-500">Password</span>
                                        <code class="rounded bg-[#F4F6FA] px-2 py-1 text-[#101D35]">00000000</code>
                                    </div>
                                </div>
                            </div>

                            <div class="rounded-lg border border-[#C5D0DE]/70 bg-white p-4">
                                <div class="mb-3 flex items-center gap-2">
                                    <Shield class="h-4 w-4 text-[#58779D]" />
                                    <span class="text-xs font-bold uppercase tracking-wider text-[#263F60]">
                                        Admin access
                                    </span>
                                </div>
                                <div class="space-y-2 text-sm">
                                    <div class="flex flex-wrap items-center justify-between gap-2">
                                        <span class="text-slate-500">Email</span>
                                        <code class="break-all rounded bg-[#F4F6FA] px-2 py-1 text-[#101D35]">admin@example.com</code>
                                    </div>
                                    <div class="flex flex-wrap items-center justify-between gap-2">
                                        <span class="text-slate-500">Password</span>
                                        <code class="rounded bg-[#F4F6FA] px-2 py-1 text-[#101D35]">00000000</code>
                                    </div>
                                    <p class="border-t border-slate-100 pt-3 text-xs leading-5 text-slate-500">
                                        These are sample credentials. They work only if the matching accounts exist in your database.
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Status Message -->
                    <div
                        v-if="status"
                        class="mb-6 rounded-xl border border-emerald-200 bg-emerald-50 p-4"
                        role="status"
                    >
                        <p class="text-sm font-medium text-emerald-800">
                            {{ status }}
                        </p>
                    </div>

                    <!-- Login Form -->
                    <Form
                        v-bind="AuthenticatedSessionController.store.form()"
                        :reset-on-success="['password']"
                        v-slot="{ errors, processing }"
                        class="space-y-5"
                    >
                        <div>
                            <Label for="email" class="mb-2 block text-sm font-semibold text-[#101D35]">
                                Email address
                            </Label>
                            <Input
                                id="email"
                                type="email"
                                name="email"
                                required
                                autofocus
                                :tabindex="1"
                                autocomplete="email"
                                placeholder="you@example.com"
                                class="h-12 w-full rounded-xl border-[#C5D0DE] bg-white px-4 text-[#101D35] placeholder:text-slate-400 focus-visible:border-[#58779D] focus-visible:ring-[#58779D]"
                            />
                            <InputError :message="errors.email" class="mt-2" />
                        </div>

                        <div>
                            <div class="mb-2 flex items-center justify-between gap-3">
                                <Label for="password" class="text-sm font-semibold text-[#101D35]">
                                    Password
                                </Label>

                                <TextLink
                                    v-if="canResetPassword"
                                    :href="request()"
                                    :tabindex="5"
                                    class="text-sm font-semibold text-[#58779D] hover:text-[#101D35] hover:underline"
                                >
                                    Forgot password?
                                </TextLink>
                            </div>

                            <Input
                                id="password"
                                type="password"
                                name="password"
                                required
                                :tabindex="2"
                                autocomplete="current-password"
                                placeholder="Enter your password"
                                class="h-12 w-full rounded-xl border-[#C5D0DE] bg-white px-4 text-[#101D35] placeholder:text-slate-400 focus-visible:border-[#58779D] focus-visible:ring-[#58779D]"
                            />
                            <InputError :message="errors.password" class="mt-2" />
                        </div>

                        <div class="flex items-center">
                            <Label for="remember" class="flex cursor-pointer items-center gap-3 text-sm text-slate-600">
                                <Checkbox
                                    id="remember"
                                    name="remember"
                                    :tabindex="3"
                                    class="rounded border-[#C5D0DE] data-[state=checked]:border-[#101D35] data-[state=checked]:bg-[#101D35]"
                                />
                                Remember me
                            </Label>
                        </div>

                        <Button
                            type="submit"
                            class="flex h-12 w-full items-center justify-center rounded-xl bg-[#101D35] px-4 font-bold text-white transition hover:bg-[#263F60]"
                            :tabindex="4"
                            :disabled="processing"
                            data-test="login-button"
                        >
                            <LoaderCircle
                                v-if="processing"
                                class="mr-2 h-5 w-5 animate-spin"
                            />
                            {{ processing ? 'Signing in...' : 'Sign In to Your Account' }}
                        </Button>

                        <div class="border-t border-[#E2E8F0] pt-5 text-center">
                            <p class="text-sm text-slate-500">
                                Don't have an account?
                                <TextLink
                                    :href="register()"
                                    :tabindex="6"
                                    class="ml-1 font-bold text-[#58779D] hover:text-[#101D35] hover:underline"
                                >
                                    Create an account
                                </TextLink>
                            </p>
                        </div>
                    </Form>

                    <!-- Security Note -->
                    <div class="mt-7 flex items-start justify-center gap-2 text-center">
                        <LockKeyhole class="mt-0.5 h-4 w-4 shrink-0 text-[#58779D]" />
                        <p class="text-xs leading-5 text-slate-500">
                            Keep your password private and sign in only through your trusted website.
                        </p>
                    </div>
                </section>
            </div>
        </main>
    </HomeLayout>
</template>
```