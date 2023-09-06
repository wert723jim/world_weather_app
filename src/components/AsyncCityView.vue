<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div v-if="route.query.preview" class="text-center text-white p-4 bg-weather-secondary w-full">
      <p>
        You are currently previewing this city, click the '+' icon to start tracking this city.
      </p>
    </div>
    <!-- weather overview -->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">
        {{ route.params.city }}
      </h1>
      <p class="text-sm mb-12">
        {{ 
          new Date(weatherData.currentTime).toLocaleDateString(
            'zh-tw',
            {
              weekday: 'short',
              day: '2-digit',
              month: 'long'
            }
          )
        }}
        {{ 
          new Date(weatherData.currentTime).toLocaleTimeString(
            'zh-tw',
            {
              timeStyle: 'short'
            }
          )
        }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(weatherData.current.temp) }}&deg;
      </p>
      <p>
        Feels like
        {{ Math.round(weatherData.current.feels_like) }} &deg;
      </p>
      <p class="capitalize">
        {{ weatherData.current.weather[0].description }}
      </p>
      <img
        :src="`https://openweathermap.org/img/wn/${weatherData.current.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>

    <hr class="w-full border-white border-opacity-10 border"/>

    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div
          v-for="hourData in weatherData.hourly"
          :key="hourData.dt"
          class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{ new Date(hourData.currentTime).toLocaleTimeString('zh-tw', {
                hour: 'numeric'
              }) }}
            </p>
            <img
             class="h-[50px] w-auto object-cover"
             :src="`https://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
             alt=""
            />
            <p class="text-xl">
              {{ Math.round(hourData.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>

    <hr class="w-full border-white border-opacity-10 border"/>

    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2>7 Days Forecast</h2>
        <div v-for="day in weatherData.daily" :key="day.dt" class="flex items-center">
          <p class="flex-1">
            {{ 
             new Date(day.dt * 1000).toLocaleDateString('zh-tw', {
              weekday:'long'
             })  
            }}
          </p>
          <img class="object-cover w-[50px] h-[50px]" :src="`https://openweathermap.org/img/wn/${day.weather[0].icon}@2x.png`" alt=""/>
          <div class="flex flex-1 justify-end">
            <p>H: {{ Math.round(day.temp.max) }}</p>
            <p>L: {{ Math.round(day.temp.min) }}</p>
          </div>
        </div>
      </div>
    </div>
    <div class="text-white flex items-center gap-2 py-12 cursor-pointer duration-150 hover:text-red-400" @click="removeCity">
      <i class="fa-solid fa-trash "></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from 'axios'
import { useRoute, useRouter } from 'vue-router'

const route = useRoute()
const router = useRouter()
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(`https://api.openweathermap.org/data/3.0/onecall?lat=${route.query.lat}&lon=${route.query.lon}&appid=fc945be1716ca9553dd9792b9881f5c8&units=metric`)
    //  本地UTC 跟 本地時區 差異(本地UTC - 本地) => 轉換成 ms
    const localOffset = new Date().getTimezoneOffset() * 60000
    // weather.data.current.dt => 目前 UTC 時間
    // 轉換成 本地時區
    const utc = weatherData.data.current.dt * 1000 + localOffset
    // 轉換成要去
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone_offset

    weatherData.data.hourly.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset
      hour.currentTime = utc + 1000 * weatherData.data.timezone_offset
    })
    return weatherData.data
  } catch (err) {
    console.log(err)
  }
}
const removeCity = () => {
  let cities = JSON.parse(localStorage.getItem('savedCities'))
  cities = cities.filter((city) => city.id !== route.query.id)
  localStorage.setItem('savedCities', JSON.stringify(cities))
  router.push({name: 'home'})
}
// invoke function
const weatherData = await getWeatherData()
</script>

<style scoped>
/* width */
::-webkit-scrollbar {
  width: 10px;
}

/* Track */
::-webkit-scrollbar-track {
  background: #f1f1f1;
}
 
/* Handle */
::-webkit-scrollbar-thumb {
  background: #888;
}

/* Handle on hover */
::-webkit-scrollbar-thumb:hover {
  background: #555;
}
</style>