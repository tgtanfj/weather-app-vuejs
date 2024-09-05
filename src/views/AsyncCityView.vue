<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const currentWeatherResult = ref(null);

const name = route.params.name;
const region = route.params.region;
const country = route.params.country;

const search = name + (region !== " " ? ", " + region : "") + ", " + country;

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.weatherapi.com/v1/current.json?key=8de480a54e914b2190a74757240409&q=${search}`
    );
    currentWeatherResult.value = weatherData?.data;
    console.log("dfadfd", currentWeatherResult.value);
    return;
  } catch (error) {
    console.log(error);
  }
};

onMounted(() => {
  getWeatherData();
});
</script>

<template>
  <div class="flex flex-col flex-1 items-center">
    <!-- Banner -->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!-- Weather Overview -->
    <div
      v-if="currentWeatherResult"
      class="flex flex-col items-center text-white py-12"
    >
      <h1 class="text-4xl mb-2">{{ name }}</h1>
      <p class="text-sm mb-12">
        {{ currentWeatherResult.location.localtime }}
      </p>
      <p class="text-8xl mb-8">
        {{ Math.round(currentWeatherResult.current.temp_c) }}&deg; C
      </p>
      <div class="text-center">
        <p>
          Feels like
          {{ Math.round(currentWeatherResult.current.feelslike_c) }}&deg; C
        </p>
        <p class="capitalize">
          {{ currentWeatherResult.current.condition.text }}
        </p>
      </div>
    </div>
  </div>
</template>
