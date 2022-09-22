<script>
    export let placeholder = '',icon = '',type='text', value = '', loading = false, on_input = ()=>{};
</script>
<div class="relative h-14 md:h-16 flex items-center">
    <input id="input" on:input={on_input} type={type} placeholder={placeholder} value={value} autocomplete="off" class="h-full transition-all pr-6 pl-16 focus-visible:outline-none input w-full rounded-2xl bg-white py-2 text-primary text-xl"/>
    {#if icon}
        <img src={icon} alt="icon" class="absolute right-6 w-8 h-8"/>
    {/if}
    <img src="/imgs/loading.svg" class={`${loading ? 'opacity-100' : 'opacity-0'} transition-all absolute left-6`} alt="loading" width="18"/>
</div>