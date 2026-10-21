```vue
<script setup lang="ts">
import HomeLayout from '@/layouts/HomeLayout.vue';
import { fleet, contact } from '@/routes';
</script>

<template>
    <HomeLayout>
        <div class="min-h-screen bg-[#F4F6FA] text-[#101D35]">

            <!-- Hero Section -->
            <section class="relative overflow-hidden bg-[#101D35] py-20 text-white md:py-28">
                <div class="absolute -right-20 -top-20 h-72 w-72 rounded-full bg-[#263F60]/50 blur-3xl"></div>
                <div class="absolute -bottom-24 -left-16 h-64 w-64 rounded-full bg-[#58779D]/30 blur-3xl"></div>

                <div class="relative mx-auto max-w-6xl px-4 text-center sm:px-6 lg:px-8">
                    <span class="mb-5 inline-block rounded-full border border-[#C5D0DE]/30 bg-white/5 px-5 py-2 text-sm font-semibold tracking-widest text-[#C5D0DE]">
                        OUR STORY
                    </span>

                    <h1 class="mb-6 text-4xl font-extrabold tracking-tight md:text-6xl">
                        About <span class="text-[#C5D0DE]">Real Rent Car</span>
                    </h1>

                    <p class="mx-auto max-w-3xl text-lg leading-8 text-slate-300 md:text-xl">
                        Your journey matters. Discover a more comfortable,
                        convenient and reliable way to travel with our vehicle
                        rental platform.
                    </p>

                    <div class="mt-9 flex flex-col justify-center gap-4 sm:flex-row">
                        <a
                            :href="fleet.url()"
                            class="rounded-xl bg-[#C5D0DE] px-8 py-3.5 font-bold text-[#101D35] transition hover:bg-white"
                        >
                            Explore Our Fleet →
                        </a>
                        <a
                            :href="contact.url()"
                            class="rounded-xl border border-white/30 px-8 py-3.5 font-semibold text-white transition hover:bg-white/10"
                        >
                            Contact Our Team
                        </a>
                    </div>
                </div>
            </section>

            <!-- Our Story -->
            <section class="mx-auto max-w-6xl px-4 py-16 sm:px-6 lg:px-8 md:py-20">
                <div class="grid items-center gap-12 lg:grid-cols-2">
                    <div>
                        <p class="mb-3 text-sm font-bold uppercase tracking-[0.2em] text-[#58779D]">
                            Who We Are
                        </p>

                        <h2 class="mb-6 text-3xl font-extrabold tracking-tight md:text-4xl">
                            Making every journey
                            <span class="text-[#58779D]">count.</span>
                        </h2>

                        <div class="space-y-5 leading-8 text-slate-600">
                            <p>
                                Real Rent Car was created with a simple mission:
                                to make vehicle rentals convenient, dependable
                                and accessible for travellers and local
                                customers alike.
                            </p>
                            <p>
                                Whether you are planning a family holiday,
                                travelling for business or need a vehicle for
                                everyday use, our platform helps you explore
                                available vehicles and arrange your reservation
                                in one place.
                            </p>
                            <p>
                                We focus on a straightforward booking
                                experience, clear rental information and a
                                customer-first approach throughout your journey.
                            </p>
                        </div>

                        <div class="mt-8 flex items-center gap-4">
                            <div class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl bg-[#101D35] text-[#C5D0DE]">
                                <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 3l8 4v5c0 5-3.5 8-8 9-4.5-1-8-4-8-9V7l8-4z"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m9 12 2 2 4-4"/>
                                </svg>
                            </div>
                            <div>
                                <h3 class="font-bold">A journey you can plan with confidence</h3>
                                <p class="mt-1 text-sm text-slate-500">
                                    Vehicle information and reservation details in one place.
                                </p>
                            </div>
                        </div>
                    </div>

                    <div class="relative">
                        <div class="absolute -inset-3 rounded-3xl bg-[#C5D0DE]/50"></div>

                        <div class="relative overflow-hidden rounded-2xl bg-[#101D35] p-8 shadow-xl md:p-10">
                            <div class="mb-8 flex items-center justify-between">
                                <div>
                                    <p class="text-sm font-medium text-slate-400">THE REAL RENT CAR EXPERIENCE</p>
                                    <h3 class="mt-2 text-2xl font-bold text-white">Travel on your terms.</h3>
                                </div>
                                <div class="flex h-14 w-14 items-center justify-center rounded-2xl bg-white/10 text-[#C5D0DE]">
                                    <svg class="h-8 w-8" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.7">
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M3 13l2-5a2 2 0 0 1 2-1h10a2 2 0 0 1 2 1l2 5v5h-2v-2H5v2H3v-5z"/>
                                        <path stroke-linecap="round" stroke-linejoin="round" d="M5 13h14M7 16h.01M17 16h.01M8 7l1-3h6l1 3"/>
                                    </svg>
                                </div>
                            </div>

                            <div class="space-y-4">
                                <div class="rounded-xl border border-white/10 bg-white/5 p-5">
                                    <p class="font-semibold text-white">Explore vehicles</p>
                                    <p class="mt-1 text-sm leading-6 text-slate-400">
                                        Compare available vehicles and review their details.
                                    </p>
                                </div>
                                <div class="rounded-xl border border-white/10 bg-white/5 p-5">
                                    <p class="font-semibold text-white">Plan your rental</p>
                                    <p class="mt-1 text-sm leading-6 text-slate-400">
                                        Select your dates and preferred pickup and return locations.
                                    </p>
                                </div>
                                <div class="rounded-xl border border-white/10 bg-white/5 p-5">
                                    <p class="font-semibold text-white">Manage reservations</p>
                                    <p class="mt-1 text-sm leading-6 text-slate-400">
                                        Access your booking information through your account.
                                    </p>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Mission and Values -->
            <section class="border-y border-[#C5D0DE]/50 bg-white py-16 md:py-20">
                <div class="mx-auto max-w-6xl px-4 sm:px-6 lg:px-8">
                    <div class="mx-auto mb-12 max-w-2xl text-center">
                        <p class="mb-3 text-sm font-bold uppercase tracking-[0.2em] text-[#58779D]">
                            What Guides Us
                        </p>
                        <h2 class="text-3xl font-extrabold md:text-4xl">
                            Our Mission & Values
                        </h2>
                        <p class="mt-4 leading-7 text-slate-500">
                            We aim to make renting a vehicle simpler through
                            dependable service, clear information and a
                            customer-focused experience.
                        </p>
                    </div>

                    <div class="grid gap-6 md:grid-cols-3">
                        <article class="rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="mb-5 flex h-14 w-14 items-center justify-center rounded-xl bg-[#101D35] text-[#C5D0DE]">
                                <svg class="h-7 w-7" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 3l8 4v5c0 5-3.5 8-8 9-4.5-1-8-4-8-9V7l8-4z"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="m9 12 2 2 4-4"/>
                                </svg>
                            </div>
                            <h3 class="mb-3 text-xl font-bold">Reliability</h3>
                            <p class="leading-7 text-slate-600">
                                We believe in dependable service and clear
                                vehicle information so customers can plan
                                their journeys with greater confidence.
                            </p>
                        </article>

                        <article class="rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="mb-5 flex h-14 w-14 items-center justify-center rounded-xl bg-[#101D35] text-[#C5D0DE]">
                                <svg class="h-7 w-7" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <circle cx="12" cy="12" r="9"/>
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 7v5l3 2"/>
                                </svg>
                            </div>
                            <h3 class="mb-3 text-xl font-bold">Transparency</h3>
                            <p class="leading-7 text-slate-600">
                                Clear rental details and pricing information
                                help customers understand their booking
                                before they proceed.
                            </p>
                        </article>

                        <article class="rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="mb-5 flex h-14 w-14 items-center justify-center rounded-xl bg-[#101D35] text-[#C5D0DE]">
                                <svg class="h-7 w-7" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                    <path stroke-linecap="round" stroke-linejoin="round" d="M12 3l2.7 5.5 6.1.9-4.4 4.3 1 6.1-5.4-2.9-5.4 2.9 1-6.1-4.4-4.3 6.1-.9L12 3z"/>
                                </svg>
                            </div>
                            <h3 class="mb-3 text-xl font-bold">Excellence</h3>
                            <p class="leading-7 text-slate-600">
                                We continually aim to improve the booking
                                experience and make vehicle reservations
                                easier for our customers.
                            </p>
                        </article>
                    </div>
                </div>
            </section>

            <!-- Why Choose Us -->
            <section class="mx-auto max-w-6xl px-4 py-16 sm:px-6 lg:px-8 md:py-20">
                <div class="mb-12 text-center">
                    <p class="mb-3 text-sm font-bold uppercase tracking-[0.2em] text-[#58779D]">
                        The Difference
                    </p>
                    <h2 class="text-3xl font-extrabold md:text-4xl">
                        Why Choose Real Rent Car?
                    </h2>
                    <p class="mx-auto mt-4 max-w-2xl leading-7 text-slate-500">
                        Designed to help you explore vehicles, plan a rental
                        and keep track of your reservations.
                    </p>
                </div>

                <div class="grid gap-5 sm:grid-cols-2 lg:grid-cols-3">
                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M3 13l2-5a2 2 0 0 1 2-1h10a2 2 0 0 1 2 1l2 5v5h-2v-2H5v2H3v-5zM5 13h14"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Vehicle Selection</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Explore vehicle details to find an option suited to your journey.
                            </p>
                        </div>
                    </div>

                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <rect x="3" y="5" width="18" height="16" rx="2"/>
                                <path stroke-linecap="round" d="M16 3v4M8 3v4M3 10h18"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Convenient Booking</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Enter rental dates and pickup details through the online booking flow.
                            </p>
                        </div>
                    </div>

                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M4 5h16v14H4zM8 9h8M8 13h5"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Booking History</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Access your reservation details through your customer account.
                            </p>
                        </div>
                    </div>

                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M12 3v18M17 7H9a3 3 0 0 0 0 6h6a3 3 0 0 1 0 6H6"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Pricing Details</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Review the displayed rental cost and booking summary before submitting.
                            </p>
                        </div>
                    </div>

                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M21 11.5a8.5 8.5 0 0 1-12.7 7.4L3 20l1.1-5.3A8.5 8.5 0 1 1 21 11.5z"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Contact Support</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Use our contact page to send questions or requests to the team.
                            </p>
                        </div>
                    </div>

                    <div class="flex gap-4 rounded-2xl border border-[#C5D0DE]/60 bg-white p-6 shadow-sm">
                        <div class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#E6EBF2] text-[#263F60]">
                            <svg class="h-6 w-6" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="1.8">
                                <path stroke-linecap="round" stroke-linejoin="round" d="M12 3l8 4v5c0 5-3.5 8-8 9-4.5-1-8-4-8-9V7l8-4z"/>
                                <path stroke-linecap="round" stroke-linejoin="round" d="m9 12 2 2 4-4"/>
                            </svg>
                        </div>
                        <div>
                            <h3 class="font-bold">Account Access</h3>
                            <p class="mt-2 text-sm leading-6 text-slate-500">
                                Sign in to use the account features available to your role.
                            </p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- Leadership -->
            <section class="border-y border-[#C5D0DE]/50 bg-white py-16 md:py-20">
                <div class="mx-auto max-w-6xl px-4 sm:px-6 lg:px-8">
                    <div class="mb-12 text-center">
                        <p class="mb-3 text-sm font-bold uppercase tracking-[0.2em] text-[#58779D]">
                            The People Behind the Platform
                        </p>
                        <h2 class="text-3xl font-extrabold md:text-4xl">
                            Our Leadership Team
                        </h2>
                        <p class="mx-auto mt-4 max-w-2xl leading-7 text-slate-500">
                            A successful rental experience depends on thoughtful
                            planning, responsive service and attention to detail.
                        </p>
                    </div>

                    <div class="grid gap-8 md:grid-cols-3">
                        <article class="overflow-hidden rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] text-center shadow-sm transition hover:-translate-y-1 hover:shadow-lg">
                            <img
                                class="h-52 w-full bg-[#E6EBF2] object-cover"
                                src="/images/team/sara.webp"
                                alt="Sarah Johnson"
                                loading="lazy"
                                @error="($event.target as HTMLImageElement).style.display = 'none'"
                            />
                            <div class="p-6">
                                <h3 class="text-xl font-bold">Sarah Johnson</h3>
                                <p class="mt-1 font-semibold text-[#58779D]">CEO & Founder</p>
                                <p class="mt-3 text-sm leading-6 text-slate-500">
                                    Focused on the company's direction and customer experience.
                                </p>
                            </div>
                        </article>

                        <article class="overflow-hidden rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] text-center shadow-sm transition hover:-translate-y-1 hover:shadow-lg">
                            <img
                                class="h-52 w-full bg-[#E6EBF2] object-cover"
                                src="/images/team/michael.webp"
                                alt="Michael Chen"
                                loading="lazy"
                                @error="($event.target as HTMLImageElement).style.display = 'none'"
                            />
                            <div class="p-6">
                                <h3 class="text-xl font-bold">Michael Chen</h3>
                                <p class="mt-1 font-semibold text-[#58779D]">Operations Director</p>
                                <p class="mt-3 text-sm leading-6 text-slate-500">
                                    Focused on vehicle operations and a smooth rental process.
                                </p>
                            </div>
                        </article>

                        <article class="overflow-hidden rounded-2xl border border-[#C5D0DE]/60 bg-[#F4F6FA] text-center shadow-sm transition hover:-translate-y-1 hover:shadow-lg">
                            <img
                                class="h-52 w-full bg-[#E6EBF2] object-cover"
                                src="/images/team/emily.webp"
                                alt="Emily Rodriguez"
                                loading="lazy"
                                @error="($event.target as HTMLImageElement).style.display = 'none'"
                            />
                            <div class="p-6">
                                <h3 class="text-xl font-bold">Emily Rodriguez</h3>
                                <p class="mt-1 font-semibold text-[#58779D]">Customer Success Manager</p>
                                <p class="mt-3 text-sm leading-6 text-slate-500">
                                    Focused on helping customers with their rental experience.
                                </p>
                            </div>
                        </article>
                    </div>

                    <p class="mx-auto mt-6 max-w-3xl text-center text-xs leading-5 text-slate-400">
                        Update the sample team names, biographies and images to match
                        your actual project information before presenting the website.
                    </p>
                </div>
            </section>

            <!-- Final Call To Action -->
            <section class="bg-[#101D35] px-4 py-16 text-center text-white md:py-20">
                <div class="mx-auto max-w-3xl">
                    <p class="mb-3 text-sm font-bold uppercase tracking-[0.2em] text-[#C5D0DE]">
                        YOUR NEXT JOURNEY STARTS HERE
                    </p>
                    <h2 class="text-3xl font-extrabold md:text-5xl">
                        Ready to hit the road?
                    </h2>
                    <p class="mx-auto mt-5 max-w-2xl leading-7 text-slate-300">
                        Explore our vehicle listings and start planning your
                        next trip with Real Rent Car.
                    </p>

                    <div class="mt-8 flex flex-col justify-center gap-4 sm:flex-row">
                        <a
                            :href="fleet.url()"
                            class="rounded-xl bg-[#C5D0DE] px-8 py-3.5 font-bold text-[#101D35] transition hover:bg-white"
                        >
                            Browse Our Fleet →
                        </a>
                        <a
                            :href="contact.url()"
                            class="rounded-xl border border-white/30 px-8 py-3.5 font-semibold text-white transition hover:bg-white/10"
                        >
                            Contact Us
                        </a>
                    </div>
                </div>
            </section>

        </div>
    </HomeLayout>
</template>
```