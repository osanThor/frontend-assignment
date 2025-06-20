<template>
  <div class="p-4">
    <div class="flex justify-between mb-4">
      <div class="flex gap-2">
        <button
          v-for="tab in tabs"
          :key="tab.value"
          @click="fetchIssues(tab.value)"
          :class="[
            tab.value === currentStatus ? 'bg-blue-600 text-white' : 'bg-gray-200',
            'rounded px-3 py-1',
          ]"
        >
          {{ tab.label }}
        </button>
      </div>
      <router-link to="/issue/new" class="bg-green-600 text-white px-4 py-1 rounded"
        >새 이슈 생성</router-link
      >
    </div>

    <ul class="flex flex-col gap-3">
      <li
        v-for="issue in issues"
        :key="issue.id"
        @click="goToDetail(issue.id)"
        class="border p-4 rounded cursor-pointer hover:bg-gray-50"
      >
        <div class="text-sm text-gray-500">{{ formatDate(issue.createdAt) }}</div>
        <div class="text-lg font-bold">{{ issue.title }}</div>
        <div class="text-sm">
          상태: {{ statusLabels[issue.status] }} | 담당자: {{ issue.user?.name || '-' }}
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'

const router = useRouter()
const issues = ref([])
const currentStatus = ref(null)

const tabs = [
  { label: '전체', value: null },
  { label: '대기', value: 'PENDING' },
  { label: '진행', value: 'IN_PROGRESS' },
  { label: '완료', value: 'COMPLETED' },
  { label: '취소', value: 'CANCELLED' },
]

const statusLabels = {
  PENDING: '대기',
  IN_PROGRESS: '진행',
  COMPLETED: '완료',
  CANCELLED: '취소',
}

const fetchIssues = async (status) => {
  currentStatus.value = status
  if (import.meta.env.VITE_MODE === 'DEV') {
    const { issues: mock } = await import('@/data/mockData')
    issues.value = status ? mock.filter((i) => i.status === status) : mock
  } else {
    const query = status ? `?status=${status}` : ''
    const res = await fetch(`http://localhost:8080/issues${query}`)
    const json = await res.json()
    issues.value = json.issues || []
  }
}

const goToDetail = (id) => router.push(`/issue/${id}`)
const formatDate = (d) => new Date(d).toLocaleString()

fetchIssues()
</script>
