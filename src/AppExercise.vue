<script setup>
import WeatherMockup from './components/weather/WeatherMockup.vue'
import WeatherComposition from './components/weather/WeatherComposition.vue';

</script>

<template>
  <div class="app-container">
    <h1>⛅ 과제 1: 날씨 (Mockup)</h1>
    <hr />
    <WeatherMockup />
  </div>

  <div>
    <h1>⛅ 과제 2: 날씨 (Composition)</h1>
    <hr>
    <WeatherComposition />
  </div>
</template>
