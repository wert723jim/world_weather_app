<template>
  <div>
    <!-- 要使用根節點，否則 router view 裡的 transition 會抓不到 -->
    <!--vue:warn Component inside <Transition> renders non-element root node that cannot be animated -->
    <Suspense>
      <!-- set template default to prevent  -->
      <template #default>
        <AsyncCityView />
      </template>
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from '../components/AsyncCityView.vue';
import CityViewSkeleton from '../components/CityViewSkeleton.vue';
</script>