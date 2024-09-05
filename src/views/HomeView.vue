<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";

const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const mapboxAPIKey = "8de480a54e914b2190a74757240409";
const searchError = ref(null);
const router = useRouter();

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.weatherapi.com/v1/search.json?key=${mapboxAPIKey}&q=${searchQuery.value}&aqi=no`
        );
        mapboxSearchResults.value = result.data;
        return;
      } catch (error) {
        console.log(error);
        searchError.value = true;
      }
    }
    mapboxSearchResults.value = null;
  }, 1000);
};

const previewCity = (searchResult) => {
  router.push({
    name: "cityView",
    params: {
      name: searchResult.name,
      region: searchResult.region ? searchResult.region : " ",
      country: searchResult.country,
    },
    query: {
      lat: searchResult.lat,
      lng: searchResult.lon,
      preview: true,
    },
  });
};
</script>

<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input
        @input="getSearchResults"
        type="text"
        v-model="searchQuery"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71] placeholder:text-[#fff]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <p v-if="searchError">Something went wrong, please try again!</p>
        <p v-if="!serverError && mapboxSearchResults.length === 0">
          No results match your query, pls try again!
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer hover:bg-weather-primary"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.name ? searchResult.name + ", " : ""
            }}{{ searchResult.region ? searchResult.region + ", " : ""
            }}{{ searchResult.country }}
          </li>
        </template>
      </ul>
    </div>
  </main>
</template>
