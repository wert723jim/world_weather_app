<template>
  <!-- w-screen don't let scroll bar reflowing-->
  <div class="flex flex-col min-h-screen font-Roboto bg-weather-primary w-screen">
    <SiteNavigation />
    <RouterView v-slot="{ Component }">
      <!-- Setting transition mode="out-in" will cause render error when click site navigation to home page-->
      <Transition name="page">
        <component :is="Component" />
      </Transition>
    </RouterView>
  </div>
</template>

<script setup>
import { RouterView } from 'vue-router'
import SiteNavigation from './components/SiteNavigation.vue'
</script>


<style scoped>
/* .page-leave-active => adding this will cause transition error*/
.page-enter-active {
  transition: 600ms ease all;
}
/* .page-leave-to =>  adding this will cause transition error*/
.page-enter-from {
  opacity: 0;
}
</style>
