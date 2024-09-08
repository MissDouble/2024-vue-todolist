<template>
    <div class="notFound">
      <h1>404 Not Found</h1>
    </div>
  </template>
  
  <script setup>
  import { onMounted } from 'vue'
  import { useRouter } from 'vue-router'
  
  const router = useRouter()
  
  onMounted(() => {
    setTimeout(() => {
      router.push('/')
    }, 1000)
  })
  </script>