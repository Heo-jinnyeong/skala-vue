<script setup>

import { ref } from 'vue'
import { ElMessage, ElMessageBox } from 'element-plus'

const userForm = ref({
    email: '',
    agree: false,
})

const handleRegister = () => {
    if (!userForm.value.email.includes('@')) {
        ElMessage.error('❌ 올바른 이메일 형식이 아닙니다.')
        return
    }
    if (!userForm.value.agree) {
        ElMessage.warning('⚠️ 이용약관에 동의하셔야 합니다.')
        return
    }
    ElMessage.success('🎉 가입 신청이 정상적으로 완료되었습니다!')
}

const productQuantity = ref(1)
const productRate = ref(4)

const downloadProgress = ref(0)
const isDownloading = ref(false)

const confirmDelete = () => {
    ElMessageBox.confirm('서버에서 해당 파일을 영구히 삭제하시겠습니까?',
    '🔥 최종 경고', {
    confirmButtonText: '네, 삭제합니다',
    cancelButtonText: '취소',
    type: 'danger',
    })
    .then(() => {
        ElMessage.success('🗑️ 파일이 안전하게 파쇄되었습니다.')
    })
    .catch(() => {
        ElMessage.info('❌ 삭제 작업이 취소되었습니다.')
})
}

const startDownload = () => {
    if (isDownloading.value) return
    isDownloading.value = true
    downloadProgress.value = 0
    
    const interval = setInterval(() => {
    downloadProgress.value += 20
    if (downloadProgress.value >= 100) {
        clearInterval(interval)
        isDownloading.value = false
        ElMessage.success('💾 대용량 데이터 로드가 완료되었습니다!')
    }
    }, 400)
}

</script>

<template>
  <el-card>
    <template #header>
      📝 실습 1. 회원가입 Form & 인풋 제어
    </template>

    <el-form :model="userForm" label-width="120px" @submit.prevent>
      <el-form-item label="이메일 주소:">
        <el-input
          v-model="userForm.email"
          type="email"
          placeholder="example@email.com"
        />
      </el-form-item>

      <el-form-item>
        <el-switch v-model="userForm.agree" />
        <span>개인정보 수집 및 필수 이용약관에 동의합니다.</span>
      </el-form-item>

      <el-button type="success" @click="handleRegister">
        🚀 회원가입하기
      </el-button>
    </el-form>
  </el-card>
  <el-card>
    <template #header>
      🛒 실습 2. 커머스 상품 수량 및 평점 시스템
    </template>

    <div>
      <span>구매 수량 선택: </span>
      <el-input-number v-model="productQuantity" :min="1" :max="10" />
      <span> (최대 10개 구매 가능)</span>
    </div>

    <div>
      <span>상품 만족도 별점: </span>
      <el-rate v-model="productRate" show-score score-template="{value}점" />
    </div>

    <p>
      🟢 실시간 장바구니 요약:
      선택 수량 {{ productQuantity }}개 /
      내가 준 점수 {{ productRate }}점
    </p>
  </el-card>

  <el-card>
    <template #header>
      ⚙️ 실습 3. 시스템 피드백 & 프로그레스 인터랙션
    </template>

    <el-button type="danger" plain @click="confirmDelete">
      🗑️ 서버 파일 삭제 테스트
    </el-button>

    <el-button type="primary" @click="startDownload">
      💾 데이터 동기화 시작
    </el-button>

    <el-progress :percentage="downloadProgress" />
  </el-card>
</template>
