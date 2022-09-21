<script>
  import Input from "./components/Input.svelte";
  import data from './data/data.json'

  let city = '';
  let cities = []
  
  const search_for_city = (event) => {
    const text = event.target.value
    city = text
    cities = data.filter(city => city.includes(text))
  }
</script>

<!-- BG ovrelay -->
<div class="absolute inset-0 w-full h-screen z-0 bg-black bg-opacity-40"/>

<main class="absolute inset-0 w-full h-screen z-50 flex items-center justify-center">  
  <div class="w-96">
    <Input on_input={search_for_city} placeholder='المدينة' />

    {#if city}
      <div class="bg-white mt-3 rounded-2xl max-h-72 overflow-y-auto p-2 z-50">
        {#each cities as city}
          <h1 class="px-6 py-2 mt-1 rounded-lg whitespace-nowrap overflow-hidden text-ellipsis hover:bg-gray-100 text-primary cursor-pointer">{city}</h1>
        {/each}
      </div>  
    {/if}
  </div>
</main>