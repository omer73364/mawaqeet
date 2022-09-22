<script>
import {fly,fade} from 'svelte/transition';

let show = false
setTimeout(() => {
    show = true
}, 500);
</script>

<div out:fade={{duration:1000}} class="w-full h-screen fixed inset-0 flex flex-col items-center justify-center bg-primary bg-opacity-95 z-50">
    {#if show}
        <img in:fly={{duration:1000}} src="./imgs/mosque.svg" alt="mosque" width="64" class="animate-pulse"/>
        <h1 in:fly={{duration:1000,delay:400}} class="text-white text-2xl mt-4">مواقيت الصلاة</h1>
    {/if}
</div>