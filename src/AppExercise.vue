<script setup>
import WeatherMockup from './components/weather/WeatherMockup.vue'
import WeatherComposition from './components/weather/WeatherComposition.vue';

</script>

<template>
  <div class="assignment-container">
    <div class="assignment-card">
      <h1>⛅ 과제 1: 날씨 (Mockup)</h1>
      <hr />
      <WeatherMockup />
    </div>

    <div class="assignment-card">
      <h1>⛅ 과제 2: 날씨 (Composition)</h1>
      <hr />
      <WeatherComposition />
    </div>
  </div>
</template>






<style scoped>
.assignment-container {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: repeat(2, minmax(0, 1fr));
  gap: 24px;
  align-items: start;
}

.assignment-card {
  padding: 24px;
  border: 1px solid #dcdfe6;
  border-radius: 12px;
  background-color: white;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.08);
}

.assignment-card h1 {
  margin-bottom: 12px;
}

.assignment-card hr {
  margin-bottom: 20px;
}

@media (max-width: 800px) {
  .assignment-container {
    grid-template-columns: 1fr;
  }
}
</style>