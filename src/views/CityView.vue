<template>
  <div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <p>Loading...</p>
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import CityViewSkeleton from "@/components/CityViewSkeleton.vue";
import AsyncCityView from "./AsyncCityView.vue";
</script>
