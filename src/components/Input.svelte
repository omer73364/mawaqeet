<script>
    export let placeholder = '',icon = '',type='text', on_input = ()=>{};
</script>
<div class="relative h-16 flex items-center">
    <input on:input={on_input} type={type} placeholder={placeholder} class="h-full transition-all px-6 focus-visible:outline-none input w-full rounded-2xl bg-white py-2 text-primary text-xl"/>
    {#if icon}
        <img src={icon} alt="icon" class="absolute right-6 w-8 h-8"/>
    {/if}
</div>