<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const API_KEY = '3c639d36e5d294d367c49733e85fcc58'

const cityOptions = [
  { label: '서울', value: 'Seoul' },
  { label: '수원', value: 'Suwon' },
  { label: '부산', value: 'Busan' },
  { label: '광주', value: 'Gwangju' },
  { label: '대구', value: 'Daegu' },
]

const selectedCity = ref('Seoul')
const weatherData = ref(null)
const forecastList = ref([])
const airQualityData = ref(null)
const isLoading = ref(false)
const errorMessage = ref('')

const getAirQualityStatus = () => {
  const value = airQualityData.value.us_aqi

  if (value <= 50) return '좋음'
  if (value <= 100) return '보통'
  if (value <= 150) return '민감군 유해'
  return '나쁨'
}

const fetchWeather = async () => {
  isLoading.value = true
  errorMessage.value = ''
  weatherData.value = null
  forecastList.value = []
  airQualityData.value = null

  try {
    // 1. OpenWeatherMap에서 현재 날씨를 가져옵니다.
    const currentResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?q=${selectedCity.value}&appid=${API_KEY}&units=metric&lang=kr`,
    )

    weatherData.value = currentResponse.data

    const latitude = weatherData.value.coord.lat
    const longitude = weatherData.value.coord.lon

    // 2. OpenWeatherMap에서 3시간 간격 예보를 가져옵니다.
    const forecastResponse = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${latitude}&lon=${longitude}&appid=${API_KEY}&units=metric&lang=kr`,
    )

    const firstForecasts = forecastResponse.data.list.slice(0, 8)

    for (const item of firstForecasts) {
      forecastList.value.push({
        time: item.dt_txt,
        temp: item.main.temp,
        status: item.weather[0].description,
        humidity: item.main.humidity,
      })
    }

    // 3. 외부 API인 Open-Meteo에서 대기질을 가져옵니다.
    const airQualityResponse = await axios.get(
      `https://air-quality-api.open-meteo.com/v1/air-quality?latitude=${latitude}&longitude=${longitude}&current=pm10,pm2_5,us_aqi&timezone=Asia%2FSeoul`,
    )

    airQualityData.value = airQualityResponse.data.current
  } catch (error) {
    console.error('과제 7 날씨 데이터 요청 실패:', error)
    errorMessage.value = '날씨 데이터를 불러오지 못했습니다.'
  } finally {
    isLoading.value = false
  }
}

onMounted(() => {
  fetchWeather()
})
</script>

<template>
  <el-card>
    <template #header>
      🌦️ 실시간 날씨 UI Library
    </template>

    <el-select v-model="selectedCity" placeholder="도시를 선택하세요.">
      <el-option
        v-for="city in cityOptions"
        :key="city.value"
        :label="city.label"
        :value="city.value"
      />
    </el-select>

    <el-button type="primary" :loading="isLoading" @click="fetchWeather">
      날씨 조회
    </el-button>
  </el-card>

  <el-skeleton v-if="isLoading" :rows="5" animated />

  <el-alert
    v-else-if="errorMessage"
    :title="errorMessage"
    type="error"
    :closable="false"
  />

  <template v-else-if="weatherData">
    <el-card>
      <template #header>
        📍 현재 날씨
      </template>

      <el-descriptions :column="1" border>
        <el-descriptions-item label="도시">
          {{ weatherData.name }}
        </el-descriptions-item>
        <el-descriptions-item label="현재 기온">
          {{ weatherData.main.temp }}℃
        </el-descriptions-item>
        <el-descriptions-item label="날씨 상태">
          <el-tag>{{ weatherData.weather[0].description }}</el-tag>
        </el-descriptions-item>
        <el-descriptions-item label="습도">
          {{ weatherData.main.humidity }}%
        </el-descriptions-item>
        <el-descriptions-item label="풍속">
          {{ weatherData.wind.speed }}m/s
        </el-descriptions-item>
      </el-descriptions>
    </el-card>

    <el-card>
      <template #header>
        📅 3시간 간격 날씨 예보
      </template>

      <el-table :data="forecastList">
        <el-table-column prop="time" label="예보 시각" />
        <el-table-column prop="temp" label="기온(℃)" />
        <el-table-column prop="status" label="날씨" />
        <el-table-column prop="humidity" label="습도(%)" />
      </el-table>
    </el-card>

    <el-card v-if="airQualityData">
      <template #header>
        🌫️ 현재 대기질
      </template>

      <el-descriptions :column="1" border>
        <el-descriptions-item label="미세먼지(PM10)">
          {{ airQualityData.pm10 }}㎍/㎥
        </el-descriptions-item>
        <el-descriptions-item label="초미세먼지(PM2.5)">
          {{ airQualityData.pm2_5 }}㎍/㎥
        </el-descriptions-item>
        <el-descriptions-item label="US AQI">
          {{ airQualityData.us_aqi }}
        </el-descriptions-item>
        <el-descriptions-item label="대기질 상태">
          <el-tag>{{ getAirQualityStatus() }}</el-tag>
        </el-descriptions-item>
      </el-descriptions>

      <p>Air quality data: Open-Meteo / CAMS</p>
    </el-card>
  </template>
</template>
