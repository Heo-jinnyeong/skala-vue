<script setup>
import { ref, onMounted, computed } from 'vue'
import { useRoute, useRouter } from 'vue-router'
import { useConfigStore } from '@/components/stores/configStore'
import axios from 'axios'
import AirQualityCard from './AirQualityCard.vue'

const route = useRoute()
const router = useRouter()
const configStore = useConfigStore()

const cityData = ref(null)
const isLoading = ref(false)

const airQualityData = ref(null)
const isAirQualityLoading = ref(false)
const airQualityError = ref('')

// 💡 [고도화] 라우터 ID 파라미터를 실제 OpenWeatherMap 쿼리용 영문 명칭과 한글 명칭으로 매핑하는 사전 장부
// const cityMapping = {
//   city_01: {
    
//     name: '대한민국 서울특별시',
//     temp: 28,
//     status: '맑음',
//     humidity: '55%',
//     wind: '2.5m/s',
//   },
//   city_02: {
//     name: '경기도 수원시 영통구',
//     temp: 24,
//     status: '비',
//     humidity: '85%',
//     wind: '4.1m/s',
//   },
//   city_03: {
//     name: '부산광역시 해운대구',
//     temp: 26,
//     status: '구름',
//     humidity: '65%',
//     wind: '5.0m/s',
//   },
//   city_04: {
//     name: '광주광역시 북구',
//     temp: 23,
//     status: '바람',
//     humidity: '60%',
//     wind: '6.2m/s',
//   },
//   city_05: {
//     name: '대구광역시 수성구',
//     temp: 31,
//     status: '폭염',
//     humidity: '45%',
//     wind: '1.8m/s',
//   },
// }

const cityMapping = {
  city_01: {
    english: 'Seoul',
    korean: '대한민국 서울특별시',
  },
  city_02: {
    english: 'Suwon',
    korean: '경기도 수원시 영통구',
  },
  city_03: {
    english: 'Busan',
    korean: '부산광역시 해운대구',
  },
  city_04: {
    english: 'Gwangju',
    korean: '광주광역시 북구',
  },
  city_05: {
    english: 'Daegu',
    korean: '대구광역시 수성구',
  },
}

const fetchAirQuality = async (latitude, longitude) => {
  isAirQualityLoading.value = true
  airQualityError.value = ''

  try {
    const response = await axios.get(
      'https://air-quality-api.open-meteo.com/v1/air-quality',
      {
        params: {
          latitude,
          longitude,
          current: 'pm10,pm2_5,us_aqi',
          timezone: 'Asia/Seoul',
        },
      },
    )

    const current = response.data.current

    airQualityData.value = {
      pm10: current.pm10,
      pm25: current.pm2_5,
      usAqi: current.us_aqi,
    }
  } catch (error) {
    airQualityError.value = '대기질 정보를 불러오지 못했습니다.'
    console.error('대기질 API 요청 실패:', error)
  } finally {
    isAirQualityLoading.value = false
  }
}

onMounted(async () => {
  const id = route.params.cityId
  const targetCity = cityMapping[id]

  if (targetCity) {
    isLoading.value = true
    try {
      const API_KEY = '3c639d36e5d294d367c49733e85fcc58'
      // 🟢 [고도화] 가짜 Mock 객체 대신, 실제 고유 타깃 도시 주소를 정밀 저격 호출
      const response = await axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${targetCity.english}&appid=${API_KEY}&units=metric&lang=kr`)

      const raw = response.data
      // 화면 템플릿 구조가 깨지지 않도록 오픈웨더 JSON 알맹이를 정확히 역매핑 유치
      cityData.value = {
        name: targetCity.korean,
        temp: raw.main.temp, // 섭씨 온도 원본 기록
        status: raw.weather[0].description,
        humidity: `${raw.main.humidity}%`,
        wind: `${raw.wind.speed}m/s`,
      }
      
      fetchAirQuality(raw.coord.lat, raw.coord.lon)

    } catch (error) {
      console.error('🔴 상세 정보 로딩 중 네트워크 에러 발생:', error)
    } finally {
      isLoading.value = false
    }
  }
})

// 🔥 [핵심 과제] 상세 정보창에서도 화씨 상태일 때 기온을 자동 변환 연산하는 센서 장착
const displayTemp = computed(() => {
  if (!cityData.value) return 0
  const rawTemp = cityData.value.temp // 원본 섭씨 온도
  if (configStore.unit === 'fahrenheit') {
    return Math.round((rawTemp * 9) / 5 + 32) // 화씨 공식 적용
  }
  return rawTemp // celsius 상태일 땐 원본 반환
})
</script>

<template>
  <div class="detail-container">
    <h3>📊 지역별 상세 기상 관측 정보 (실시간 데이터 연동)</h3>
    <hr />

    <div v-if="isLoading" style="text-align: center; padding: 20px 0; color: #7f8c8d">데이터베이스로부터 상세 정보를 동기화하는 중입니다...</div>

    <template v-else>
  <template v-if="cityData">
    <div class="info-card">
      <h4>📍 지정 지역: {{ cityData.name }}</h4>

      <p>
        실시간 기온:
        <strong>{{ displayTemp }}{{ configStore.unitSymbol }}</strong>
      </p>

      <p>기상 현황: {{ cityData.status }}</p>
      <p>대기 습도: {{ cityData.humidity }}</p>
      <p>현재 풍속: {{ cityData.wind }}</p>
    </div>

    <p v-if="isAirQualityLoading">
      대기질 정보를 불러오는 중입니다...
    </p>

    <p v-else-if="airQualityError">
      {{ airQualityError }}
    </p>

    <AirQualityCard
      v-else-if="airQualityData"
      :air-quality="airQualityData"
    />
  </template>

  <div v-else>
    <p>해당 지역의 상세 데이터 장부가 존재하지 않거나 에러가 발생했습니다.</p>
  </div>
</template>

    <button @click="router.push('/axios')" class="back-btn">← 메인 대시보드로 돌아가기</button>
  </div>
</template>

<style scoped>
.detail-container {
  margin: 0 auto;
  background: white;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.06);
}
.info-card {
  background: #f1f2f6;
  padding: 15px;
  border-radius: 6px;
  margin: 15px 0;
}
.back-btn {
  padding: 8px 12px;
  background: #2c3e50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>