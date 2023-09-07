<template>
  <div v-for="city in savedCities" :key="city.id">
    <CityCard :city="city" @click="goToCityView(city)"/>
  </div>
  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import CityCard from './CityCard.vue'
const router = useRouter()
const savedCities = ref([])
const getCities = async () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))

    const requests = []
    savedCities.value.forEach((city) => {
      requests.push(
        axios.get(`https://api.openweathermap.org/data/2.5/weather?lat=${city.coords.lat}&lon=${city.coords.lon}&appid=fc945be1716ca9553dd9792b9881f5c8&&units=metric`)
      )
    })

    const weatherData = await Promise.all(requests)

    // flicker delay
    await new Promise((res) => setTimeout(res, 500))

    weatherData.forEach((value, index) => {
      // 將 API 回傳值
      savedCities.value[index].weather = value.data
    })
  }
}
await getCities()
const goToCityView = (city) => {
  router.push({
    name: 'city',
    params: {city: city.city, state: city.state},
    query: {
      lat: city.coords.lat,
      lon: city.coords.lon,
      id: city.id
    }
  })
}
  

</script>