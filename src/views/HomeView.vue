<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:outline-none focus:border-weather-secondary focus:shadow-[0px_1px_0_0_#004E71]"
      >
      <p v-if="searchError" class="text-red">Something went wrong, please try again</p>
      <template v-else>
        <ul
          class="absolute bg-weather-secondary w-full shadow-md top-[66px] py-2 px-1"
          v-if="searchResults"
        >
          <p v-if="searchQuery && !searchResults.length" class="py-2">No result</p>
          <li
            v-for="result in searchResults"
            :key="result.id"
            class="py-2 cursor-pointer"
            @click="previewCity(result)"
          >
            {{ result.place_name }}
          </li>
        </ul>
      </template>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
  </main>
</template>

<script setup>
import { ref } from 'vue'
import axios from 'axios'
import { useRouter } from 'vue-router'
import CityList from '../components/CityList.vue'
import CityCardSkeleton from '../components/CityCardSkeleton.vue';
const mapBox_token = import.meta.env.VITE_MAPBOX_TOKEN
const router = useRouter()
const searchQuery = ref('')
const queryTimeout = ref(null)
const searchResults = ref(null)
const searchError = ref(null)

// lazy search => search (call api when user stop typing)
const getSearchResults = () => {
  searchError.value = null
  clearTimeout(queryTimeout.value)
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value) {
      try {
        const { data } = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?types=place&access_token=${mapBox_token}`)
        searchResults.value = data.features
      } catch {
        searchError.value = true
      }
      return
    }
    searchResults.value = null
  }, 500)
}
const previewCity = (result) => {
  const [city, state] = result.place_name.split(',')
  router.push({
    name: 'city',
    params: { city: city, state: state.replaceAll(' ', '') },
    query: {
      lat: result.geometry.coordinates[1],
      lon: result.geometry.coordinates[0],
      preview: true
    }
  })
}
</script>
