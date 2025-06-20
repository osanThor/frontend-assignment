<template>
  <div>{{ id }}</div>
</template>

<script setup>
/*
TODO: 이 컴포넌트를 재사용 가능하게 설계해보세요.
- 같은 폼 구조와 로직을 재사용하여 코드 중복을 방지하세요.
*/
import { ref, onMounted } from 'vue'
import { useRoute, useRouter } from 'vue-router'

const router = useRouter()
const route = useRoute()
const id = route.params.id
</script>
