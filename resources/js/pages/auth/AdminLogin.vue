```vue
<script setup lang="ts">
import AuthenticatedSessionController from '@/actions/App/Http/Controllers/Auth/AuthenticatedSessionController';
import InputError from '@/components/InputError.vue';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { Form, Head } from '@inertiajs/vue3';
import { LoaderCircle, LockKeyhole, ShieldCheck, CarFront } from 'lucide-vue-next';

defineProps<{
    status?: string;
    canResetPassword: boolean;
}>();
</script>

<template>
    <div>
        <Head title="Admin Access" />

        <div class="relative flex min-h-screen items-center justify-center overflow-hidden bg-[#F4F6FA] px-4 py-12 sm:px-6">
            <!-- Background decoration -->
            <div class="pointer-events-none absolute -right-24 -top-24 h-80 w-80 rounded-full bg-[#58779D]/10 blur-3xl"></div>
            <div class="pointer-events-none absolute -bottom-24 -left-24 h-80 w-80 rounded-full bg-[#263F60]/10 blur-3xl"></div>

            <div class="relative grid w-full max-w-5xl overflow-hidden rounded-3xl border border-[#C5D0DE] bg-white shadow-2xl lg:grid-cols-2">

                <!-- Admin branding panel -->
                <div class="relative flex flex-col justify-between overflow-hidden bg-[#101D35] p-8 text-white sm:p-12">
                    <div class="absolute -right-16 -top-16 h-56 w-56 rounded-full border border-white/10"></div>
                    <div class="absolute -right-8 -top-8 h-40 w-40 rounded-full border border-white/10"></div>

                    <div class="relative flex items-center gap-3">
                        <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#C5D0DE] text-[#101D35]">
                            <CarFront class="h-7 w-7" />
                        </span>
                        <div>
                            <p class="font-bold tracking-wide">REAL RENT CAR</p>
                            <p class="text-xs text-slate-400">Administration Portal</p>
                        </div>
                    </div>

                    <div class="relative my-14">
                        <span class="inline-flex items-center gap-2 rounded-full border border-[#C5D0DE]/30 bg-white/5 px-4 py-2 text-sm text-[#C5D0DE]">
                            <ShieldCheck class="h-4 w-4" />
                            Restricted access
                        </span>

                        <h1 class="mt-7 text-4xl font-bold leading-tight sm:text-5xl">
                            Control your
                            <span class="block text-[#C5D0DE]">operations.</span>
                        </h1>

                        <p class="mt-5 max-w-sm leading-7 text-slate-300">
                            Access the administration portal to manage your
                            vehicle rental platform.
                        </p>

                        <div class="mt-10 space-y-4 text-sm text-slate-300">
                            <div class="flex items-center gap-3">
                                <ShieldCheck class="h-5 w-5 text-[#C5D0DE]" />
                                Protected administrator sign-in
                            </div>
                            <div class="flex items-center gap-3">
                                <LockKeyhole class="h-5 w-5 text-[#C5D0DE]" />
                                Authentication through Laravel
                            </div>
                        </div>
                    </div>

                    <p class="relative text-xs text-slate-400">
                        Authorized administrators only.
                    </p>
                </div>

                <!-- Login form -->
                <div class="flex items-center justify-center p-6 sm:p-10 lg:p-12">
                    <div class="w-full max-w-md">
                        <div class="mb-8">
                            <div class="mb-5 flex h-14 w-14 items-center justify-center rounded-2xl bg-[#E8EDF4] text-[#263F60]">
                                <LockKeyhole class="h-7 w-7" />
                            </div>

                            <p class="text-sm font-semibold uppercase tracking-[0.2em] text-[#58779D]">
                                Admin sign in
                            </p>
                            <h2 class="mt-2 text-3xl font-bold tracking-tight text-[#101D35]">
                                Welcome back
                            </h2>
                            <p class="mt-3 text-sm leading-6 text-slate-600">
                                Enter your administrator credentials to continue.
                            </p>
                        </div>

                        <Form
                            v-bind="AuthenticatedSessionController.storeAdminLogin.form()"
                            :reset-on-success="['password']"
                            v-slot="{ errors, processing }"
                            class="space-y-6"
                        >
                            <div>
                                <Label for="email" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                    Administrator email
                                </Label>
                                <Input
                                    id="email"
                                    type="email"
                                    name="email"
                                    required
                                    autofocus
                                    :tabindex="1"
                                    autocomplete="username"
                                    placeholder="admin@example.com"
                                    class="h-12 w-full rounded-xl border-[#C5D0DE] bg-[#F8FAFC] px-4 text-[#101D35] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="errors.email" class="mt-2 text-sm" />
                            </div>

                            <div>
                                <Label for="password" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                    Password
                                </Label>
                                <Input
                                    id="password"
                                    type="password"
                                    name="password"
                                    required
                                    :tabindex="2"
                                    autocomplete="current-password"
                                    placeholder="Enter your password"
                                    class="h-12 w-full rounded-xl border-[#C5D0DE] bg-[#F8FAFC] px-4 text-[#101D35] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="errors.password" class="mt-2 text-sm" />
                            </div>

                            <div v-if="status" class="rounded-xl border border-[#C5D0DE] bg-[#F4F6FA] p-3 text-sm text-[#263F60]">
                                {{ status }}
                            </div>

                            <Button
                                type="submit"
                                class="flex h-12 w-full items-center justify-center rounded-xl bg-[#101D35] px-4 font-semibold text-white transition duration-200 hover:bg-[#263F60] disabled:cursor-not-allowed disabled:opacity-50"
                                :tabindex="3"
                                :disabled="processing"
                                data-test="admin-login-button"
                            >
                                <LoaderCircle
                                    v-if="processing"
                                    class="mr-2 h-5 w-5 animate-spin"
                                />
                                {{ processing ? 'Authenticating...' : 'Sign in to Admin Portal' }}
                            </Button>

                            <div class="border-t border-[#E2E8F0] pt-5 text-center">
                                <div class="flex items-center justify-center gap-2 text-[#58779D]">
                                    <ShieldCheck class="h-4 w-4" />
                                    <p class="text-xs font-medium">
                                        Protected administrator sign-in
                                    </p>
                                </div>
                                <p class="mt-2 text-xs leading-5 text-slate-500">
                                    Only authorized users should access this portal.
                                </p>
                            </div>
                        </Form>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>
```