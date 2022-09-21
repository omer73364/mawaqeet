<script>
  import Input from "./components/Input.svelte";
  import data from './data/data.json'
  import {blur,slide} from 'svelte/transition';
  import LazyList from 'lazy-load-list/svelte'

  let search = '';
  let cities = []
  let show_input = true
  let loading = false
  
  const search_for_city = (event) => {
    const text = event.target.value
    search = text
    cities = data.filter(city => city.includes(text))
  }

  const get_data = async(link) => {
    loading = true
    const result = await (await fetch(link)).json()
    loading = false
    show_input = false
    console.log(result)
  }

  const choose_city = (value) => {
    search = value
    cities = []
    const [city,country] = value.split('-')
    const link = `http://api.aladhan.com/v1/timingsByCity?city=${city}&country=${country}&method=8`
    get_data(link)
  }
</script>

<!-- BG ovrelay -->
<div class="absolute backdrop-blur-[3px] inset-0 w-full h-screen z-0 bg-black bg-opacity-40"/>

<main class="absolute inset-0 w-full h-screen z-50 flex items-center justify-center px-8">  
  <div class="w-96 relative">
    
    {#if show_input}
      <div out:blur={{duration: 500}}>
        <Input 
          placeholder='المدينة' 
          value={search}
          loading={loading}
          on_input={search_for_city} 
        />
      </div>
    {/if}

    {#if search && show_input && !loading}
      <div in:slide={{duration: 200}} out:slide={{duration: 200}} class={`absolute top-16 w-full bg-white mt-3 transition-all rounded-2xl ${cities.length >= 10 ? 'h-72' : 'h-auto'} overflow-y-auto p-2 z-50`}>
        
        {#if cities?.length}
          <LazyList
            data={cities}
            itemsPerRender={10}
            let:item={city}
            defaultLoading={false}
          >
            <h1 on:click={() => choose_city(city)} class="px-6 py-2 mt-1 rounded-lg select-none whitespace-nowrap overflow-hidden text-ellipsis hover:bg-gray-100 text-primary cursor-pointer">{city}</h1>
            <img slot="loading" src="/imgs/loading.svg" alt="loading" width="18"/>
          </LazyList>
        {:else}
          <h1 class="px-6 py-2 mt-1 rounded-lg select-none whitespace-nowrap overflow-hidden text-ellipsis text-primary">
            لا يوجد مدينة بهذا الاسم
          </h1>
        {/if}

      </div>  
    {/if}
    
  </div>
</main>