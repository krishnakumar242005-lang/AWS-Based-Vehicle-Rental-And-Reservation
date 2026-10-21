
<script setup lang="ts">
import HomeLayout from '@/layouts/HomeLayout.vue';
import { Head, Link, usePage } from '@inertiajs/vue3';
import { fleet } from '@/routes';
import { about } from '@/routes';

interface Car {
    id: number;
    make: string;
    model: string;
    year: number;
    price_per_day: string | number;
    description?: string;
    fuel_type?: string;
    image_url?: string;
    image?: string;
    color?: string;
    status?: string;
}

const page = usePage();
const homeCars = (page.props.homeCars as Car[] | undefined) ?? [];

const carImage = (car: Car) =>
    car.image_url || car.image || '/images/hero_image.png';

const formatPrice = (price: string | number) => {
    const amount = Number(price);
    return Number.isFinite(amount)
        ? new Intl.NumberFormat('en-IN', {
              maximumFractionDigits: 0,
          }).format(amount)
        : String(price);
};
</script>

<template>
    <Head title="RealRentCar | Premium Car Rental" />

    <HomeLayout>
        <div class="overflow-hidden bg-[#F4F6FA] text-[#101D35]">

            <!-- HERO -->
            <section class="relative isolate overflow-hidden bg-[#101D35] text-white">
                <div class="absolute inset-0 -z-10 bg-gradient-to-br from-[#101D35] via-[#172B48] to-[#263F60]"></div>
                <div class="absolute -right-24 -top-24 -z-10 h-80 w-80 rounded-full bg-white/5 blur-3xl"></div>
                <div class="absolute -bottom-32 left-1/3 -z-10 h-96 w-96 rounded-full bg-[#9FB4CE]/10 blur-3xl"></div>

                <div class="mx-auto grid max-w-7xl items-center gap-8 px-5 py-16 sm:px-8 sm:py-20 lg:grid-cols-2 lg:gap-12 lg:px-8 lg:py-24">
                    <div class="relative z-10">
                        <div class="inline-flex items-center gap-2 rounded-full border border-white/20 bg-white/5 px-4 py-2 text-xs font-bold uppercase tracking-[0.2em] text-[#D5E0ED]">
                            <span class="h-2 w-2 rounded-full bg-[#C5D0DE]"></span>
                            Your journey starts here
                        </div>

                        <h1 class="mt-7 max-w-2xl text-4xl leading-tight font-black tracking-tight sm:text-5xl lg:text-6xl">
                            Drive your
                            <span class="text-[#C5D0DE]">dream.</span>
                            <br />
                            Own the
                            <span class="text-[#A8BED8]">journey.</span>
                        </h1>

                        <p class="mt-6 max-w-xl text-base leading-8 text-slate-300 sm:text-lg">
                            Find the right car for every journey. Explore our fleet
                            and enjoy a simple, comfortable, and reliable rental
                            experience.
                        </p>

                        <div class="mt-9 flex flex-col gap-3 sm:flex-row">
                            <Link
                                :href="fleet()"
                                class="inline-flex items-center justify-center gap-3 rounded-xl bg-[#C5D0DE] px-7 py-4 font-bold text-[#101D35] shadow-xl transition hover:-translate-y-1 hover:bg-white"
                            >
                                Explore Our Fleet
                                <span class="text-xl" aria-hidden="true">→</span>
                            </Link>

                            <Link
                                :href="about()"
                                class="inline-flex items-center justify-center rounded-xl border border-white/25 px-7 py-4 font-bold text-white transition hover:bg-white/10"
                            >
                                Discover Our Story
                            </Link>
                        </div>

                        <div class="mt-10 grid max-w-md grid-cols-3 gap-4 border-t border-white/15 pt-7">
                            <div>
                                <p class="text-2xl font-black sm:text-3xl">Easy</p>
                                <p class="mt-1 text-xs text-slate-400 sm:text-sm">Reservations</p>
                            </div>
                            <div>
                                <p class="text-2xl font-black sm:text-3xl">Flexible</p>
                                <p class="mt-1 text-xs text-slate-400 sm:text-sm">Travel plans</p>
                            </div>
                            <div>
                                <p class="text-2xl font-black sm:text-3xl">Simple</p>
                                <p class="mt-1 text-xs text-slate-400 sm:text-sm">Booking flow</p>
                            </div>
                        </div>
                    </div>

                    <div class="relative mx-auto w-full max-w-2xl">
                        <div class="absolute inset-6 rounded-full bg-[#A8BED8]/10 blur-3xl"></div>

                        <div class="relative overflow-hidden rounded-3xl border border-white/15 bg-white/5 p-3 shadow-2xl backdrop-blur-sm sm:p-5">
                            <img
                                src="/images/hero_image.png"
                                alt="Premium car ready for your next journey"
                                class="h-64 w-full rounded-2xl object-cover sm:h-80 lg:h-[410px]"
                            />

                            <div class="absolute bottom-6 left-6 right-6 rounded-2xl border border-white/20 bg-[#101D35]/90 p-4 backdrop-blur-md sm:bottom-8 sm:left-8 sm:right-8 sm:p-5">
                                <div class="flex items-center justify-between gap-4">
                                    <div>
                                        <p class="text-xs font-semibold uppercase tracking-[0.18em] text-[#C5D0DE]">
                                            Your next adventure
                                        </p>
                                        <p class="mt-2 text-lg font-bold sm:text-xl">
                                            Choose your perfect ride
                                        </p>
                                    </div>
                                    <Link
                                        :href="fleet()"
                                        class="flex h-11 w-11 shrink-0 items-center justify-center rounded-xl bg-[#C5D0DE] text-xl font-bold text-[#101D35] transition hover:bg-white"
                                        aria-label="Browse available vehicles"
                                    >
                                        →
                                    </Link>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>

                <div class="h-1 bg-gradient-to-r from-[#101D35] via-[#C5D0DE] to-[#101D35]"></div>
            </section>

            <!-- BENEFIT STRIP -->
            <section class="relative z-10 mx-auto -mt-1 max-w-7xl px-5 sm:px-8 lg:px-8">
                <div class="grid gap-px overflow-hidden rounded-2xl border border-[#DCE3ED] bg-[#DCE3ED] shadow-lg sm:grid-cols-3">
                    <div class="flex items-center gap-4 bg-white p-5 sm:p-6">
                        <div class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl bg-[#E8EDF5] text-2xl">🚘</div>
                        <div>
                            <h2 class="font-bold">Choice of Vehicles</h2>
                            <p class="mt-1 text-sm text-slate-500">Find a car for your needs</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-4 bg-white p-5 sm:p-6">
                        <div class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl bg-[#E8EDF5] text-2xl">📅</div>
                        <div>
                            <h2 class="font-bold">Simple Reservations</h2>
                            <p class="mt-1 text-sm text-slate-500">Plan your dates with ease</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-4 bg-white p-5 sm:p-6">
                        <div class="flex h-12 w-12 shrink-0 items-center justify-center rounded-xl bg-[#E8EDF5] text-2xl">🤝</div>
                        <div>
                            <h2 class="font-bold">Customer Support</h2>
                            <p class="mt-1 text-sm text-slate-500">Help when you need it</p>
                        </div>
                    </div>
                </div>
            </section>

            <!-- FEATURED CARS -->
            <section class="mx-auto max-w-7xl px-5 py-20 sm:px-8 lg:px-8 lg:py-24">
                <div class="flex flex-col justify-between gap-5 sm:flex-row sm:items-end">
                    <div>
                        <p class="text-sm font-black uppercase tracking-[0.2em] text-[#58779D]">
                            Find your ride
                        </p>
                        <h2 class="mt-3 text-3xl font-black tracking-tight sm:text-4xl">
                            Explore our <span class="text-[#58779D]">fleet.</span>
                        </h2>
                        <p class="mt-4 max-w-xl leading-7 text-slate-600">
                            From everyday drives to special occasions, discover a
                            vehicle that fits your plans.
                        </p>
                    </div>

                    <Link
                        :href="fleet()"
                        class="inline-flex items-center gap-2 self-start rounded-xl border border-[#C5D0DE] bg-white px-5 py-3 font-bold text-[#101D35] transition hover:border-[#101D35] hover:bg-[#E8EDF5] sm:self-auto"
                    >
                        View All Cars <span aria-hidden="true">→</span>
                    </Link>
                </div>

                <div v-if="homeCars.length" class="mt-10 grid gap-6 sm:grid-cols-2 lg:grid-cols-3">
                    <article
                        v-for="car in homeCars"
                        :key="car.id"
                        class="group overflow-hidden rounded-2xl border border-[#DCE3ED] bg-white shadow-sm transition duration-300 hover:-translate-y-1 hover:shadow-xl"
                    >
                        <div class="relative overflow-hidden bg-[#E8EDF5]">
                            <img
                                :src="carImage(car)"
                                :alt="`${car.make} ${car.model}`"
                                class="h-56 w-full object-cover transition duration-500 group-hover:scale-105"
                                loading="lazy"
                            />
                            <span class="absolute left-4 top-4 rounded-full border border-white/70 bg-white/95 px-3 py-1.5 text-xs font-bold text-[#101D35]">
                                {{ car.year }}
                            </span>
                        </div>

                        <div class="p-5">
                            <p class="text-xs font-bold uppercase tracking-[0.16em] text-[#58779D]">
                                {{ car.make }}
                            </p>
                            <h3 class="mt-2 text-xl font-black">
                                {{ car.model }}
                            </h3>

                            <p v-if="car.description" class="mt-2 line-clamp-2 text-sm leading-6 text-slate-500">
                                {{ car.description }}
                            </p>

                            <div class="mt-5 flex items-center justify-between gap-3 border-t border-slate-100 pt-5">
                                <div>
                                    <p class="text-xl font-black text-[#101D35]">
                                        ₹{{ formatPrice(car.price_per_day) }}
                                    </p>
                                    <p class="text-xs text-slate-500">per day</p>
                                </div>
                                <Link
                                    :href="fleet()"
                                    class="rounded-xl bg-[#101D35] px-4 py-3 text-sm font-bold text-white transition hover:bg-[#263F60]"
                                >
                                    View Details
                                </Link>
                            </div>
                        </div>
                    </article>
                </div>

                <div v-else class="mt-10 rounded-2xl border border-dashed border-[#C5D0DE] bg-white px-6 py-12 text-center">
                    <div class="mx-auto flex h-16 w-16 items-center justify-center rounded-2xl bg-[#E8EDF5] text-3xl">🚘</div>
                    <h3 class="mt-5 text-xl font-black">Your next ride awaits</h3>
                    <p class="mx-auto mt-2 max-w-md leading-7 text-slate-500">
                        Browse the complete fleet to find a vehicle for your journey.
                    </p>
                    <Link
                        :href="fleet()"
                        class="mt-6 inline-flex rounded-xl bg-[#101D35] px-6 py-3 font-bold text-white transition hover:bg-[#263F60]"
                    >
                        Browse All Vehicles
                    </Link>
                </div>
            </section>

            <!-- WHY CHOOSE US -->
            <section class="bg-white">
                <div class="mx-auto max-w-7xl px-5 py-20 sm:px-8 lg:px-8 lg:py-24">
                    <div class="mx-auto max-w-2xl text-center">
                        <p class="text-sm font-black uppercase tracking-[0.2em] text-[#58779D]">
                            The RealRentCar difference
                        </p>
                        <h2 class="mt-3 text-3xl font-black tracking-tight sm:text-4xl">
                            A better way to <span class="text-[#58779D]">travel.</span>
                        </h2>
                        <p class="mt-4 leading-7 text-slate-600">
                            A clear, convenient rental experience from choosing
                            your vehicle to planning your journey.
                        </p>
                    </div>

                    <div class="mt-12 grid gap-6 md:grid-cols-3">
                        <article class="rounded-2xl border border-[#DCE3ED] bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="flex h-14 w-14 items-center justify-center rounded-2xl bg-[#101D35] text-2xl text-white">✦</div>
                            <h3 class="mt-6 text-xl font-black">Vehicles for Every Journey</h3>
                            <p class="mt-3 leading-7 text-slate-600">
                                Explore the available fleet and find a vehicle
                                suited to your travel plans.
                            </p>
                        </article>

                        <article class="rounded-2xl border border-[#DCE3ED] bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="flex h-14 w-14 items-center justify-center rounded-2xl bg-[#101D35] text-2xl text-white">◷</div>
                            <h3 class="mt-6 text-xl font-black">Convenient Booking</h3>
                            <p class="mt-3 leading-7 text-slate-600">
                                Choose your rental dates and follow a simple
                                reservation process.
                            </p>
                        </article>

                        <article class="rounded-2xl border border-[#DCE3ED] bg-[#F4F6FA] p-7 transition hover:-translate-y-1 hover:shadow-lg">
                            <div class="flex h-14 w-14 items-center justify-center rounded-2xl bg-[#101D35] text-2xl text-white">✓</div>
                            <h3 class="mt-6 text-xl font-black">Clear Information</h3>
                            <p class="mt-3 leading-7 text-slate-600">
                                Review vehicle details and displayed rental
                                prices before making your reservation.
                            </p>
                        </article>
                    </div>
                </div>
            </section>

            <!-- FINAL CALL TO ACTION -->
            <section class="px-5 py-16 sm:px-8 lg:px-8 lg:py-20">
                <div class="relative mx-auto max-w-7xl overflow-hidden rounded-3xl bg-[#101D35] px-6 py-12 text-center text-white sm:px-12 sm:py-16">
                    <div class="absolute -right-16 -top-24 h-72 w-72 rounded-full bg-white/5 blur-3xl"></div>
                    <div class="absolute -bottom-24 -left-16 h-64 w-64 rounded-full bg-[#A8BED8]/10 blur-3xl"></div>

                    <div class="relative">
                        <p class="text-sm font-bold uppercase tracking-[0.22em] text-[#C5D0DE]">
                            Your journey begins here
                        </p>
                        <h2 class="mx-auto mt-4 max-w-3xl text-3xl font-black tracking-tight sm:text-4xl lg:text-5xl">
                            Ready to find your perfect ride?
                        </h2>
                        <p class="mx-auto mt-5 max-w-2xl leading-7 text-slate-300">
                            Explore the fleet, compare available vehicles, and
                            start planning your next journey with RealRentCar.
                        </p>
                        <div class="mt-8 flex flex-col justify-center gap-3 sm:flex-row">
                            <Link
                                :href="fleet()"
                                class="rounded-xl bg-[#C5D0DE] px-7 py-4 font-bold text-[#101D35] transition hover:bg-white"
                            >
                                Explore Vehicles →
                            </Link>
                            <Link
                                :href="about()"
                                class="rounded-xl border border-white/25 px-7 py-4 font-bold text-white transition hover:bg-white/10"
                            >
                                Learn About Us
                            </Link>
                        </div>
                    </div>
                </div>
            </section>

        </div>
    </HomeLayout>
</template>
