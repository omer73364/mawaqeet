<script>
  import Input from "./components/Input.svelte";
  import {blur,slide} from 'svelte/transition';
  import LazyList from 'lazy-load-list/svelte'
  import Loading from './components/Loading.svelte'
  import { onMount } from "svelte";

  let time = new Date().toLocaleTimeString();

  let search = ''
  let data = []
  let cities = []
  let show_input = true
  let is_loading = false
  let is_getting_data = true
  let mawaqeet_data
  const arabic_months = [
    'ياناير',
    'فبراير',
    'مارس',
    'أبريل',
    'مايو',
    'يونيو',
    'يوليو',
    'أغسطس',
    'سبتمبر',
    'أكتوبر',
    'نوفمير',
    'ديسمبر',
  ]

  const get_arabic_month = (month) => arabic_months[month-1]
  const format_time = (time) => {
    const result = time
    let [t,a] = result.split(' ')
    t = t.split(':')
    t.pop()
    t = t.join(':')
    a = a.toLocaleLowerCase() == 'pm' ? 'م' : 'ص'
    return `${t} ${a}`
  }
  
  const search_for_city = (event) => {
    const text = event.target.value
    search = text
    cities = data.filter(city => city.toLocaleLowerCase().includes(text.toLocaleLowerCase()))
  }

  const get_data = async(link) => {
    is_loading = true
    const result = await (await fetch(link)).json()
    is_loading = false
    show_input = false
    console.log(result)
    mawaqeet_data = {
      city: search.split('-')[0],
      date:result?.data?.date?.gregorian?.day + '/' + get_arabic_month(result?.data?.date?.gregorian?.month?.number) + '/' + result?.data?.date?.gregorian?.year + 'م',
      hijri: result?.data?.date?.hijri?.day + '/' + result?.data?.date?.hijri?.month?.ar + '/' + result?.data?.date?.hijri?.year + 'هـ',
      timezone: result?.data?.meta?.timezone
    }
  }

  const choose_city = (value) => {
    search = value
    cities = []
    const [city,country] = value.split('-')
    const link = `http://api.aladhan.com/v1/timingsByCity?city=${city}&country=${country}&method=8`
    get_data(link)
  }

  const get_countries = async() => {
    is_getting_data = true
    data = await (await fetch('./data/data.json')).json()
    setTimeout(() => {
      is_getting_data = false
    }, 3000);
  }

  const change_city = () => {
    search_for_city({target:{value:search.split('-')[0]}})
    show_input = true
    setTimeout(() => {
      document.getElementById('input').focus()
    }, 200);
  }

  onMount(()=>{
    get_countries()
    const interval = setInterval(() => {
			time = new Date().toLocaleTimeString('en-US',mawaqeet_data?.timezone && {timeZone: mawaqeet_data?.timezone});
		}, 1000);

		return () => {
			clearInterval(interval);
		};
  })
</script>

<!-- BG ovrelay -->
<div class="absolute backdrop-blur-[3px] inset-0 w-full h-screen z-0 bg-black bg-opacity-40"/>

<main class="absolute inset-0 w-full h-screen z-50 flex items-center justify-center px-8">  
  <div class="w-96 relative">
  {#if mawaqeet_data && !show_input}
    <!-- Render data -->
    <div in:slide={{duration:500,delay:800}} class="w-full rounded-2xl bg-primary py-6 px-8 flex flex-col relative">
      <img on:click={change_city} class="absolute cursor-pointer top-6 left-8" src="./imgs/pin.svg" alt="pin" width="30"/>
      <h1 class="text-white text-4xl">{format_time(time)}</h1>
      <h1 class="text-white text-6xl my-4">{mawaqeet_data.city}</h1>
      <h1 class="text-white opacity-80 text-2xl mb-1">{mawaqeet_data.hijri}</h1>
      <h1 class="text-white opacity-80 text-2xl mb-1">{mawaqeet_data.date}</h1>
    </div>
  {:else}
    <!-- Search input -->
    {#if show_input && !is_getting_data}
      <div out:blur={{duration: 500}}>
        <h1 class="text-4xl text-white mb-4 text-center">مواقيت الصلاة</h1>
        <Input 
          placeholder='المدينة' 
          value={search}
          loading={is_loading}
          on_input={search_for_city} 
        />
      </div>
    {/if}
    <!-- Select cities -->
    {#if search && show_input && !is_loading}
      <div in:slide={{duration: 200}} out:slide={{duration: 200}} class={`absolute top-[7.5rem] w-full bg-white mt-3 transition-all rounded-2xl ${cities.length >= 10 ? 'h-72' : 'h-auto'} overflow-y-auto p-2 z-50`}>
        
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
  {/if}
  </div>
</main>

<!-- Full screen loading -->
{#if is_getting_data}
  <Loading/>
{/if}