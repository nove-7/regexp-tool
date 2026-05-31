<script setup>
const props = defineProps({
  replaceResult: {
    type: String,
    default: '',
  },
})
</script>
<template>
  <div class="replace-result" v-html="replaceResult"></div>
</template>
<style>
.replace-result {
  margin: 10px;
  /* background-color: #794040; */
  height: 100%;
}
</style>
