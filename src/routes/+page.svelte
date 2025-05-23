

<style>
    @import "tailwindcss";
    @import "$lib/styles/custom.css";
    

    .background {
        background-image: url('/background.jpg');
        background-size: cover;
        background-position: center;
    }
</style>

<div class="background min-h-screen w-full relative">
    <!-- Heading at the top center -->
    <header class="w-full py-8 flex justify-center">
        <h1 class="text-5xl font-extrabold text-white drop-shadow-lg bg-black/40 px-8 py-2 rounded-lg">
            Video Management App
        </h1>
    </header>

    <!-- Hamburger and nav at top right -->
    <div class="absolute top-8 right-8 flex flex-col items-end z-20">
        <button id="hamburger" class="bg-black/60 hover:bg-black/80 rounded-full p-4 shadow-lg text-white focus:outline-none transition-all duration-200">
            <svg class="w-12 h-12" fill="none" stroke="currentColor" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7"></path>
            </svg>
        </button>
        <nav id="nav-menu" class="hidden flex-col items-end mt-4 bg-black/70 rounded-lg p-4 shadow-lg">
            <a href="/SearchandRescue" class="nav-link text-white py-2 px-4 rounded hover:bg-amber-400/80 hover:text-black transition">Search and Rescue</a>
            <a href="/AnadarkoSurveillance" class="nav-link text-white py-2 px-4 rounded hover:bg-amber-400/80 hover:text-black transition">Anadarko Surveillance</a>
            <a href="/Delete" class="nav-link text-white py-2 px-4 rounded hover:bg-amber-400/80 hover:text-black transition">Delete</a>
            <a href="/" class="nav-link text-white py-2 px-4 rounded hover:bg-amber-400/80 hover:text-black transition">Link 4</a>
            <a href="/" class="nav-link text-white py-2 px-4 rounded hover:bg-amber-400/80 hover:text-black transition">Link 5</a>
        </nav>
    </div>
</div>

<script>
    import { onMount } from 'svelte';
    //import search from './SearchandRescue/+page.svelte';

    onMount(() => {
        const hamburger = document.getElementById('hamburger');
        const navMenu = document.getElementById('nav-menu');

        hamburger.addEventListener('click', () => {
            navMenu.classList.toggle('hidden');
        });
    });
</script>