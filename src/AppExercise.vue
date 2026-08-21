<script setup>
import WeatherMockup from './components/weather/WeatherMockup.vue'
import WeatherComposition from './components/weather/WeatherComposition.vue';
import WeatherParent from './components/weather/WeatherParent.vue';
import UnitToggler from './components/weather/UnitToggler.vue';

</script>

<template>
  <div class="app-container">
    <h1>⛅ 과제 1: 날씨 (Mockup)</h1>
    <hr />
    <WeatherMockup />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 2: 날씨 (컴포지션)</h1>
    <hr />
    <WeatherComposition />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 3: 날씨 (컴포넌트)</h1>
    <hr />
    <WeatherParent />
  </div>
  <div class="app-container">
    <h1>⛅ 과제 4: 라우터적용</h1>
    <hr />
    <div class="dashboard-wrapper">
      <nav class="navigation-bar">
        <RouterLink to="/" class="nav-item">🌦️ 날씨 대시보드</RouterLink>
        <span class="divider">|</span>
        <RouterLink to="/about" class="nav-item">ℹ️ 서비스 소개</RouterLink>
      </nav>
      <main>
        <RouterView />
        <!-- RouterView v-slot="{ Component }">
          <KeepAlive>
            <component :is="Component" />
          </KeepAlive>
        </RouterView-->
      </main>
    </div>
  </div>
  <div class="app-container">
    <h1>⛅ 과제 5: 스토어적용</h1>
    <hr />
    <div class="dashboard-wrapper">
      <nav class="navigation-bar">
        <RouterLink to="/" class="nav-item">🌦️ 날씨 대시보드</RouterLink>
        <span class="divider">|</span>
        <RouterLink to="/about" class="nav-item">ℹ️ 서비스 소개</RouterLink>
        <UnitToggler />
      </nav>
      <main>
        <RouterView />
      </main>
    </div>
    <div class="app-container">
  <h1>⛅ 과제 6: Axios API 적용</h1>
  <hr />

  <div class="dashboard-wrapper">
    <nav class="navigation-bar">
      <RouterLink to="/axios" class="nav-item">
        🌦️ 실시간 날씨 대시보드
      </RouterLink>

      <span class="divider">|</span>

      <RouterLink to="/about" class="nav-item">
        ℹ️ 서비스 소개
      </RouterLink>

      <UnitToggler />
    </nav>

    <main>
      <RouterView />
    </main>
    </div>
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