<template >
<div class="w-[800px] h-[400px] bg-slate-400 mx-auto relative mt-[100px]"> 
   <div class="w-[800px] h-[400px] relative">
    <img :src="backgroundImageUrl" alt="" class="object-cover overflow-y-hidden w-full h-full"> 
    <div class="absolute top-0 left-0 flex flex-col items-center ml-[80px] mt-[200px] gap-4 text-white"> 
      <div>
        <h1 v-if="weatherData" class="text-5xl font-bold font-mono ">{{ weatherData.name }}</h1>
      </div>
      <div class="flex gap-4">
     <h1 v-if="weatherData && weatherData.main" class="text-6xl">
  {{ getTempInCelsius(weatherData.main.temp) }}°</h1>
  <div>
  <h1 v-if="weatherData && weatherData.weather" class="text-xl font-bold ">{{ weatherData.weather[0].main}}</h1>
  <span v-if="weatherData && weatherData.weather" class="text-sm">{{ estTime }}</span>
</div>
<span v-if="weatherData">
  <Icon :icon="`${iconUrl.library}:${iconUrl.name}`" width="64" height="64" style="color: white" />
</span>
</div></div>
  </div>
  <div class="absolute top-0 right-0 h-[400px] w-[300px] glassmorphism text-white">
    <div class="flex items-center justify-center h-[60px] gap-2">
    <input type="text" placeholder="search location..." v-model="search" class="w-[180px] h-[30px] bg-inherit pl-4 border-b-2 text-white border-x-white " >
    <button @click="searchPlace" class="text-3xl"><Icon icon="ic:outline-search"  style="color: white" /></button>
  </div>
  <h3 class="font-mono text-lg  text-center font-bold tracking-wider">Weather Informations</h3>
  <h3 v-if="weatherData && weatherData.weather" class="text-lg font-mono text-center mt-2">{{ weatherData.weather[0].description}}</h3>
  <div v-if="weatherData" class="flex flex-col gap-4 mx-6 mt-4">
    <div class="flex justify-between">
     <span class="font-mono">Temp Max</span>
      <div class="flex gap-2 items-center">
    <span> {{ getTempInCelsius(weatherData.main.temp_max) }}°C </span>
    <Icon icon="ion:thermometer-outline"  />
   </div>
  </div>
  <div class="flex justify-between">
    <span class="font-mono">Temp Min</span>
    <div class="flex gap-2 items-center">
    <span > {{ getTempInCelsius(weatherData.main.temp_min) }}°C </span>
    <span><Icon icon="wi:thermometer" style="color: white"/></span>
  </div>
  </div>
  <div class="flex justify-between">
    <span class="font-mono">Humidity</span>
    <div class="flex gap-2 items-center">
    <span >{{ weatherData.main.humidity}}% </span>
    <span><Icon icon="lets-icons:humidity-light"  style="color: white" /></span>
  </div>
  </div>
  <div class="flex justify-between">
    <span class="font-mono">Wind</span>
    <div class="flex gap-2 items-center">
    <span > {{ weatherData.wind.speed}}km/hr </span>
    <span><Icon icon="ph:wind-bold"  style="color: white" /></span>
  </div>
  </div>
  <div class="flex justify-between">
    <span class="font-mono">Cloudy</span>
    <div class="flex gap-2 items-center">
    <span > {{ weatherData.clouds.all}}% </span>
    <span><Icon icon="lucide:cloudy"  style="color: white" /></span>
  </div>
  </div>
  </div>
  </div>
  </div>
</template>

<script setup>
import {ref, computed } from 'vue';
import { Icon } from '@iconify/vue';
import axios from 'axios';
import Cloudy from "@/assets/cloudy.jpg"
import Rain from "@/assets/raincast.jpg"
import Clear from "@/assets/clearone.jpg"
import Snow from "@/assets/snow.png"
import Smoke from "@/assets/smoke.jpg"
import ThunderStorm from "@/assets/thunderstorm.jpg"
import Mist from "@/assets/mist.jpeg"
import Haze from "@/assets/haze.jpg"
import Drizzle from "@/assets/drizzle.webp"
import Default from "@/assets/default.jpg"

const weatherData = ref(null);
const error = ref(null);
const search = ref(null)
const  getTempInCelsius = (Kelvin) =>{
  return Math.floor(Kelvin-273.15)
}
const weatherMapping = {
  'Clear': Clear,
  'Rain': Rain,
  'Clouds': Cloudy,
   'Snow': Snow,
   'Smoke': Smoke,
   'Thunderstorm': ThunderStorm,
   'Mist': Mist,
   'Haze': Haze,
   'Drizzle': Drizzle
};
const IconMapping = {
  'Clear': { library: 'fluent-mdl2', name: 'sunny' },
  'Rain': { library: 'raphael', name: 'rain' },
  'Clouds': { library: 'fluent-mdl2', name: 'cloudy' },
   'Snow':{ library: 'mingcute', name: 'snow-line' },
   'Smoke': { library: 'wi', name: 'smoke' },
   'Thunderstorm': { library: 'ic', name: 'baseline-thunderstorm' },
   'Mist': { library: 'ri', name: 'mist-line' },
   'Haze':{ library: 'bi', name: 'cloud-haze2-fill' },
   'Drizzle': { library: 'f7', name: 'cloud-drizzle' },
};

const iconUrl = computed(() => {
  const currentIcon = weatherData.value?.weather?.[0]?.main;
  return IconMapping[currentIcon] ;
});
const backgroundImageUrl = computed(() => {
  const currentWeather = weatherData.value?.weather?.[0]?.main;
  return weatherMapping[currentWeather] || Default; 
});
const today = new Date();
const options = { timeZone: 'America/New_York', hour12: false, hour: '2-digit', minute: '2-digit',  year: '2-digit', month: 'short', day: 'numeric', weekday: 'short' };
const estTime = today.toLocaleString('en-US', options);

 const searchPlace = async() =>{
  const apiKey = import.meta.env.VITE_API_KEY
  const apiUrl = import.meta.env.VITE_API_URL
  await axios.get(apiUrl, {
    params: {
      q: search.value,
      appid: apiKey
    }
  })
  .then(response => {
    weatherData.value = response.data;
    console.log(weatherData.value);
  })
  .catch(err => {
    error.value = err;
    console.error(err);
  });
};
</script>
