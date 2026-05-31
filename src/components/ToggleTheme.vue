<script setup>
import { ref } from 'vue'

const isDark = ref(false)

const toggleDark = () => {
  isDark.value = !isDark.value

  document.documentElement.classList.toggle('dark', isDark.value)
}
</script>
<template>
  <el-button @click="toggleDark"> 切换暗黑模式 </el-button>
</template>
