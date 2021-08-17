<template>
  <q-layout view="hHh lpR fFf">

    <q-header class="bg-primary text-white" elevated>
      <q-toolbar>
        <q-btn dense flat icon="fas fa-bars" round @click="toggleLeftDrawer"/>

        <q-toolbar-title>
          <q-avatar class="gt-sm">
            <img src="https://cdn.quasar.dev/logo-v2/svg/logo-mono-white.svg">
          </q-avatar>
          <span class="gt-sm">
            WeatherApp
          </span>
        </q-toolbar-title>
        <q-input v-model="searchLocation" :dense="true" class="q-mr-lg" input-class="text-white"
                 label="Search weather..."
                 label-color="secondary"
                 placeholder="Madrid,es">
          <template v-slot:append>
            <q-icon v-if="searchLocation !== ''" class="cursor-pointer" color="white" name="fas fa-times"
                    @click="searchLocation = ''"/>
            <q-btn flat round @click="checkWeatherFromInput">
              <q-icon color="white" name="fas fa-search"/>
            </q-btn>
          </template>
        </q-input>
        <q-btn class="q-mx-md" color="secondary" push round>
          <q-icon name="fas fa-location-arrow" size="xs"/>
        </q-btn>
      </q-toolbar>
    </q-header>
    <!-- Menú Lateral -->
    <q-drawer v-model="leftDrawerOpen" behavior="desktop" elevated overlay side="left">
      <div class="q-pa-md" style="max-width: 350px">
        <q-list bordered separator>
          <q-item v-for="location in locations" :key="location.name" v-ripple clickable
                  @click="checkWeatherFromSideBar(location)">
            <q-item-section>
              <q-item-label>{{ location.name }}</q-item-label>
              <q-item-label caption>{{ location.island }}</q-item-label>
            </q-item-section>
            <q-item-section avatar>
              <q-icon color="primary" name="fas fa-map-marker-alt"/>
            </q-item-section>
          </q-item>
        </q-list>
      </div>
    </q-drawer>

    <q-page-container>
      <router-view/>
    </q-page-container>

  </q-layout>
</template>

<script>
import { provide, ref } from 'vue'

const locations = require('../assets/locations.json')
const OPENWEATHER_API_KEY = process.env.OPENWEATHER_API_KEY

export default {
  setup () {
    const leftDrawerOpen = ref(false)
    const weatherData = ref(null)
    const searchLocation = ref('')
    const checkWeatherFromSideBar = async (item) => {
      const uri = 'https://api.openweathermap.org/data/2.5/weather' +
        `?lat=${item.location.lat}` +
        `&lon=${item.location.lon}` +
        `&units=metric&&lang=es&appid=${OPENWEATHER_API_KEY}`
      const res = await fetch(uri)
      weatherData.value = await res.json()
      weatherData.value.island = item.island
      leftDrawerOpen.value = !leftDrawerOpen.value
      searchLocation.value = item.name
      console.log(await weatherData.value)
    }
    const checkWeatherFromInput = async () => {
      const uri = 'https://api.openweathermap.org/data/2.5/weather' +
        `?q=${searchLocation.value}&units=metric&&lang=es&appid=${OPENWEATHER_API_KEY}`
      const res = await fetch(uri)
      weatherData.value = await res.json()
      console.log(await weatherData.value)
    }

    provide('weatherData', weatherData)

    return {
      locations,
      searchLocation,
      leftDrawerOpen,
      toggleLeftDrawer () {
        leftDrawerOpen.value = !leftDrawerOpen.value
      },
      checkWeatherFromSideBar,
      checkWeatherFromInput
    }
  }
}
</script>
