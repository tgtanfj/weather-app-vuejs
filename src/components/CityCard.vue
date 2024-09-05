<template>
  <div
    v-if="city"
    class="flex py-6 px-3 bg-weather-secondary rounded-md shadow-md cursor-pointer"
  >
    <div class="flex flex-col flex-1">
      <h2 class="text-3xl">{{ city.location.name }}</h2>
      <h3 class="text-3xl">
        {{ city.location.region ? city.location.region : "Somewhere" }}
      </h3>
    </div>
    <div class="flex flex-col gap-2">
      <p class="text-3xl self-end">
        {{ Math.round(city.current.temp_c) }}&deg;C
      </p>
      <div class="flex gap-2">
        <span class="text-xs">
          Feels like: {{ Math.round(city.current.feelslike_c) }}&deg;C
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
defineProps({
  city: {
    type: Object,
    default: () => {},
  },
});
</script>
