<template>
  <header class="sticky bg-weather-primary shadow-lg">
    <nav class="container flex flex-col sm:flex-row items-center gap-4 py-6 text-white">
      <RouterLink :to="{name: 'home'}">
        <div class="flex gap-3">
          <i class="fa-solid fa-meteor text-2xl"></i>
          <p class="text-2xl">The Local Weather</p>
        </div>
      </RouterLink>
      
      <div class="flex flex-1 justify-end gap-4">
        <i
          class="fa-solid fa-circle-info text-xl hover:text-weather-secondary cursor-pointer"
          @click="toggleModal"
        ></i>
        <i class="fa-solid fa-plus text-xl hover:text-weather-secondary cursor-pointer" @click="addCities" v-if="route.query.preview"></i>
      </div>

      <BaseModal
        :modalActive="modalActive"
        @closeModal="toggleModal"
      >
        <div class="text-black">
          <h1 class="text-2xl mb-1">About:</h1>
          <p class="mb-4">
            The Local Weather allows you to track the current and
            future weather of cities of your choosing.
          </p>
          <h2 class="text-2xl">How it works:</h2>
          <ol class="list-decimal list-inside mb-4">
            <li>
              Search for your city by entering the name into the
              search bar.
            </li>
            <li>
              Select a city within the results, this will take
              you to the current weather for your selection.
            </li>
            <li>
              Track the city by clicking on the "+" icon in the
              top right. This will save the city to view at a
              later time on the home page.
            </li>
          </ol>

          <h2 class="text-2xl">Removing a city</h2>
          <p>
            If you no longer wish to track a city, simply select
            the city within the home page. At the bottom of the
            page, there will be am option to delete the city.
          </p>
        </div>
      </BaseModal>
    </nav>
  </header>
  
</template>

<script setup>
import { ref } from 'vue'
import { uid } from 'uid'
import { RouterLink, useRoute, useRouter } from 'vue-router'
import BaseModal from './BaseModal.vue'

const modalActive = ref(null)
const savedCities = ref([])
const route = useRoute()
const router = useRouter()

const toggleModal = () => {
  modalActive.value = !modalActive.value
}

const addCities = () => {
  if (localStorage.getItem('savedCities')) {
    savedCities.value = JSON.parse(localStorage.getItem('savedCities'))
  }

  const locationObj = {
    id: uid(),
    city: route.params.city,
    state: route.params.state,
    coords: {
      lat: route.query.lat,
      lon: route.query.lon
    }
  }

  savedCities.value.push(locationObj)

  localStorage.setItem('savedCities', JSON.stringify(savedCities.value))

  let query = {...route.query}
  console.log(query)
  delete query.preview
  router.replace({ query })
}
</script>