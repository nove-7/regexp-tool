<script setup>
import { computed } from 'vue'
import MatchListItem from './MatchListItem.vue'
const props = defineProps({
  matchResultIndex: {
    type: Array,
    default: () => [],
  },
  matchHighlight: {
    type: Array,
    default: () => [],
  },
  groupHighlight: {
    type: Array,
    default: () => [],
  },
})
const data = computed(() => {
  return props.matchResultIndex?.map((item, index) => {
    return {
      matchResultIndex: item,
      matchHighlight: props.matchHighlight[index],
      groupHighlight: { ...props.groupHighlight[index] },
    }
  })
})
console.log(data.value)
</script>
<template>
  <div class="match-list">
    <MatchListItem
      v-for="(item, index) in data"
      :key="item.matchResultIndex"
      :index="index"
      :data="item"
    />
  </div>
</template>
<style scoped>
/* .match-list {
  height: 100%;

  overflow-y: auto;
  overflow-x: hidden;

  padding: 12px;

  box-sizing: border-box;

  display: flex;
  flex-direction: column;
  gap: 12px;
} */
.match-list {
  flex: 1;

  overflow-y: auto;
  overflow-x: hidden;

  padding: 12px;

  display: flex;
  flex-direction: column;
  gap: 12px;

  min-height: 0;
}
</style>
