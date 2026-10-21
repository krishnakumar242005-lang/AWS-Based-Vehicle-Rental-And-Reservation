```vue
<script setup lang="ts">
import RegisteredUserController from '@/actions/App/Http/Controllers/Auth/RegisteredUserController';
import HomeLayout from '@/layouts/HomeLayout.vue';
import InputError from '@/components/InputError.vue';
import TextLink from '@/components/TextLink.vue';
import { Button } from '@/components/ui/button';
import { Input } from '@/components/ui/input';
import { Label } from '@/components/ui/label';
import { login } from '@/routes';
import { Form, Head } from '@inertiajs/vue3';
import { LoaderCircle, UserPlus, ShieldCheck, CarFront } from 'lucide-vue-next';
</script>

<template>
    <HomeLayout>
        <Head title="Create Account" />

        <div class="min-h-screen bg-[#F4F6FA] px-4 py-10 sm:px-6 lg:py-16">
            <div class="mx-auto grid w-full max-w-6xl overflow-hidden rounded-3xl border border-[#C5D0DE] bg-white shadow-xl lg:grid-cols-2">

                <!-- Left brand panel -->
                <div class="relative flex flex-col justify-between overflow-hidden bg-[#101D35] p-8 text-white sm:p-12 lg:min-h-[760px]">
                    <div class="absolute -right-20 -top-20 h-64 w-64 rounded-full border border-white/10"></div>
                    <div class="absolute -right-10 -top-10 h-44 w-44 rounded-full border border-white/10"></div>

                    <div class="relative">
                        <a href="/" class="inline-flex items-center gap-3">
                            <span class="flex h-12 w-12 items-center justify-center rounded-xl bg-[#C5D0DE] text-[#101D35]">
                                <CarFront class="h-7 w-7" />
                            </span>
                            <span class="text-xl font-bold tracking-wide">REAL RENT CAR</span>
                        </a>
                    </div>

                    <div class="relative my-12">
                        <span class="inline-flex items-center gap-2 rounded-full border border-[#C5D0DE]/30 bg-white/5 px-4 py-2 text-sm text-[#C5D0DE]">
                            <UserPlus class="h-4 w-4" />
                            Join our community
                        </span>

                        <h1 class="mt-6 text-4xl font-bold leading-tight sm:text-5xl">
                            Your next journey
                            <span class="block text-[#C5D0DE]">starts here.</span>
                        </h1>

                        <p class="mt-5 max-w-md leading-7 text-slate-300">
                            Create your account to explore our fleet, make reservations,
                            and manage your car rental journeys in one place.
                        </p>

                        <div class="mt-10 space-y-5">
                            <div class="flex items-start gap-4">
                                <span class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg bg-white/10 text-[#C5D0DE]">
                                    <CarFront class="h-5 w-5" />
                                </span>
                                <div>
                                    <h3 class="font-semibold">Explore our fleet</h3>
                                    <p class="mt-1 text-sm leading-6 text-slate-300">
                                        Discover vehicles for your travel needs.
                                    </p>
                                </div>
                            </div>

                            <div class="flex items-start gap-4">
                                <span class="flex h-10 w-10 shrink-0 items-center justify-center rounded-lg bg-white/10 text-[#C5D0DE]">
                                    <ShieldCheck class="h-5 w-5" />
                                </span>
                                <div>
                                    <h3 class="font-semibold">Manage your bookings</h3>
                                    <p class="mt-1 text-sm leading-6 text-slate-300">
                                        Keep your reservations organised in one place.
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>

                    <p class="relative text-xs text-slate-400">
                        Drive your plans forward with Real Rent Car.
                    </p>
                </div>

                <!-- Registration form -->
                <div class="flex items-center justify-center p-6 sm:p-10 lg:p-12">
                    <div class="w-full max-w-md">
                        <div class="mb-8">
                            <div class="mb-5 flex h-14 w-14 items-center justify-center rounded-2xl bg-[#E8EDF4] text-[#263F60]">
                                <UserPlus class="h-7 w-7" />
                            </div>

                            <p class="text-sm font-semibold uppercase tracking-[0.2em] text-[#58779D]">
                                Get started
                            </p>
                            <h2 class="mt-2 text-3xl font-bold tracking-tight text-[#101D35]">
                                Create your account
                            </h2>
                            <p class="mt-3 text-sm leading-6 text-slate-600">
                                Fill in your details below to get started.
                            </p>
                        </div>

                        <Form
                            v-bind="RegisteredUserController.store.form()"
                            :reset-on-success="[
                                'password',
                                'password_confirmation',
                            ]"
                            v-slot="{ errors, processing }"
                            class="space-y-5"
                        >
                            <div>
                                <Label for="name" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                    Full name
                                </Label>
                                <Input
                                    id="name"
                                    type="text"
                                    required
                                    autofocus
                                    :tabindex="1"
                                    autocomplete="name"
                                    name="name"
                                    placeholder="Enter your full name"
                                    class="h-12 w-full rounded-xl border-[#C5D0DE] bg-[#F8FAFC] px-4 text-[#101D35] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="errors.name" class="mt-2 text-sm" />
                            </div>

                            <div>
                                <Label for="email" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                    Email address
                                </Label>
                                <Input
                                    id="email"
                                    type="email"
                                    required
                                    :tabindex="2"
                                    autocomplete="email"
                                    name="email"
                                    placeholder="you@example.com"
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
                                    required
                                    :tabindex="3"
                                    autocomplete="new-password"
                                    name="password"
                                    placeholder="Create a password"
                                    class="h-12 w-full rounded-xl border-[#C5D0DE] bg-[#F8FAFC] px-4 text-[#101D35] focus-visible:ring-[#58779D]"
                                />
                                <InputError :message="errors.password" class="mt-2 text-sm" />
                            </div>

                            <div>
                                <Label for="password_confirmation" class="mb-2 block text-sm font-semibold text-[#263F60]">
                                    Confirm password
                                </Label>
                                <Input
                                    id="password_confirmation"
                                    type="password"
                                    required
                                    :tabindex="4"
                                    autocomplete="new-password"
                                    name="password_confirmation"
                                    placeholder="Enter your password again"
                                    class="h-12 w-full rounded-xl border-[#C5D0DE] bg-[#F8FAFC] px-4 text-[#101D35] focus-visible:ring-[#58779D]"
                                />
                                <InputError
                                    :message="errors.password_confirmation"
                                    class="mt-2 text-sm"
                                />
                            </div>

                            <Button
                                type="submit"
                                class="flex h-12 w-full items-center justify-center rounded-xl bg-[#101D35] px-4 font-semibold text-white transition duration-200 hover:bg-[#263F60]"
                                tabindex="5"
                                :disabled="processing"
                                data-test="register-user-button"
                            >
                                <LoaderCircle
                                    v-if="processing"
                                    class="mr-2 h-5 w-5 animate-spin"
                                />
                                {{ processing ? 'Creating account...' : 'Create account' }}
                            </Button>

                            <div class="border-t border-[#E2E8F0] pt-5 text-center">
                                <p class="text-sm text-slate-600">
                                    Already have an account?
                                    <TextLink
                                        :href="login()"
                                        class="font-semibold text-[#263F60] underline-offset-4 hover:text-[#58779D] hover:underline"
                                        :tabindex="6"
                                    >
                                        Sign in
                                    </TextLink>
                                </p>
                            </div>
                        </Form>

                        <p class="mt-6 text-center text-xs leading-5 text-slate-500">
                            Please use an email address you can access.
                        </p>
                    </div>
                </div>
            </div>
        </div>
    </HomeLayout>
</template>
```