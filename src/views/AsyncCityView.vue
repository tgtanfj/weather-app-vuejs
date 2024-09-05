<script setup>
import axios from "axios";
import { onMounted, ref } from "vue";
import { useRoute } from "vue-router";

const route = useRoute();
const currentWeatherResult = ref(null);
const weatherByHour = ref(null);
const weather7Days = ref(null);

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
    return;
  } catch (error) {
    console.log(error);
  }
};

const getWeatherByHour = async () => {
  try {
    const weather = await axios.get(
      `https://api.weatherapi.com/v1/forecast.json?key=8de480a54e914b2190a74757240409&q=${search}&days=1&aqi=no&alerts=no`
    );
    if (weather) {
      weatherByHour.value = weather?.data;
      return;
    }
    return;
  } catch (error) {
    console.log(error);
  }
};

const getWeather7Days = async () => {
  try {
    const weather = await axios.get(
      `https://api.weatherapi.com/v1/forecast.json?key=8de480a54e914b2190a74757240409&q=${search}&days=7&aqi=no&alerts=no`
    );
    if (weather) {
      weather7Days.value = weather?.data;
      console.log("weather7Days", weather7Days.value);
      return;
    }
    return;
  } catch (error) {
    console.log(error);
  }
};

onMounted(() => {
  getWeatherData();
  getWeatherByHour();
  getWeather7Days();
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
        {{
          new Date(currentWeatherResult.location.localtime).toLocaleTimeString(
            "en-us",
            {
              timeStyle: "short",
            }
          )
        }}
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
        <img
          :src="currentWeatherResult.current.condition.icon"
          class="w-[150px] h-auto"
          alt=""
        />
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <!-- Hourly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather</h2>
        <div class="flex gap-10 overflow-x-scroll pb-6">
          <div
            v-if="weatherByHour"
            v-for="hourData in weatherByHour.forecast.forecastday[0].hour"
            :key="weatherByHour.time_epoch"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md">
              {{
                new Date(hourData.time).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              :src="hourData.condition.icon"
              class="w-auto h-[50px] object-cover"
              alt=""
            />
            <p class="text-xl">{{ Math.round(hourData.temp_c) }}&deg;C</p>
          </div>
        </div>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <!-- Weekly Weather -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">7 Day Forecast</h2>
        <div
          v-if="weather7Days"
          v-for="day in weather7Days.forecast.forecastday"
          :key="day.date_epoch"
          class="flex items-center"
        >
          <p class="flex-1">
            {{
              new Date(day.date_epoch * 1000).toLocaleDateString("en-us", {
                weekday: "long",
              })
            }}
          </p>
          <img
            :src="day.day.condition.icon"
            class="w-[50px] h-[50px] object-cover"
            alt=""
          />
          <div class="flex gap-2 flex-1 justify-end">
            <p>H: {{ Math.round(day.day.maxtemp_c) }}</p>
            <p>L: {{ Math.round(day.day.mintemp_c) }}</p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
