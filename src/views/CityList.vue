<template>
  <div v-for="city in savedCities" :key="city.location.localtime_epoch">
    <CityCard :city="city" @click="goToCityView(city)" />
  </div>

  <p v-if="savedCities.length === 0">
    No locations added. To start tracking a location, search in the field above.
  </p>
</template>

<script setup>
import CityCard from "@/components/CityCard.vue";
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";

const savedCities = ref([]);
const router = useRouter();

const goToCityView = (city) => {
  router.push({
    name: "cityView",
    params: {
      name: city.location.name,
      region: city.location.region ? city.location.region : " ",
      country: city.location.country,
    },
    query: {
      id: city.id,
    },
  });
};

const getCities = async () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));

    const requests = [];

    savedCities.value.forEach((city) => {
      const location =
        city.name +
        (city.region !== " " ? ", " + city.region : "") +
        ", " +
        city.country;

      requests.push(
        axios.get(
          `https://api.weatherapi.com/v1/current.json?key=8de480a54e914b2190a74757240409&q=${location}&aqi=no`
        )
      );
    });

    const weatherData = await Promise.all(requests);

    await new Promise((res) => setTimeout(res, 1000));

    weatherData.forEach((value, index) => {
      const currentCity = savedCities.value[index];
      savedCities.value[index] = {
        ...value.data,
        id: currentCity.id,
      };
    });
  }
};
await getCities();
</script>
