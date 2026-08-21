<script setup>
import { computed } from 'vue'

const props = defineProps({
  airQuality: {
    type: Object,
    required: true,
  },
})

const aqiInfo = computed(() => {
  const value = props.airQuality.usAqi

  if (value <= 50) {
    return { label: '좋음', className: 'good' }
  }
  if (value <= 100) {
    return { label: '보통', className: 'moderate' }
  }
  if (value <= 150) {
    return { label: '민감군 유해', className: 'sensitive' }
  }
  if (value <= 200) {
    return { label: '나쁨', className: 'unhealthy' }
  }
  if (value <= 300) {
    return { label: '매우 나쁨', className: 'very-unhealthy' }
  }

  return { label: '위험', className: 'hazardous' }
})
</script>

<template>
  <div class="air-quality-card">
    <h4>🌫️ 현재 대기질</h4>

    <p>미세먼지(PM10): {{ airQuality.pm10 }}㎍/㎥</p>
    <p>초미세먼지(PM2.5): {{ airQuality.pm25 }}㎍/㎥</p>
    <p>US AQI: {{ airQuality.usAqi }}</p>

    <p :class="aqiInfo.className">
      대기질 상태: <strong>{{ aqiInfo.label }}</strong>
    </p>

    <small>
      Air quality data: Open-Meteo / CAMS
    </small>
  </div>
</template>