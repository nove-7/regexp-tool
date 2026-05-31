<script setup>
defineProps({
  data: {
    type: Object,
  },
  index: {
    type: Number,
    default: 0,
  },
})
</script>
<template>
  <div class="match-item">
    <div class="match-header">
      <span>匹配 {{ index + 1 }}</span>
      <span>index: {{ data.matchResultIndex }}</span>
    </div>

    <div class="match-content">
      {{ data.matchHighlight }}
    </div>

    <div class="groups">
      <div v-for="(group, groupIndex) in data.groupHighlight" :key="groupIndex" class="group-item">
        <span class="group-tag"> ${{ +groupIndex + 1 }} </span>

        <span class="group-value">
          {{ group }}
        </span>
      </div>
    </div>
  </div>
</template>
<style scoped>
.match-item {
  background: #252526;
  border: 1px solid #3c3c3c;
  border-radius: 8px;

  overflow: hidden;

  transition: all 0.2s;

  flex-shrink: 0;
}

.match-item:hover {
  border-color: #007acc;
}

.match-header {
  display: flex;
  justify-content: space-between;
  align-items: center;

  padding: 10px 14px;

  background: #2d2d30;
  border-bottom: 1px solid #3c3c3c;

  font-size: 13px;
  color: #cccccc;
}

.match-content {
  padding: 14px;

  font-family: var(--mono);
  font-size: 14px;
  line-height: 1.6;

  color: #dcdcaa;

  word-break: break-all;
}

/* 捕获组容器 */
.groups {
  display: flex;
  flex-wrap: wrap;

  padding: 0 14px 14px;

  column-gap: 18px;
  row-gap: 8px;
}

/* 单个捕获组 */
.group-item {
  display: inline-flex;
  align-items: center;

  gap: 4px;
}

/* [$1] */
.group-tag {
  padding: 2px 6px;

  border-radius: 4px;

  background: rgba(86, 156, 214, 0.15);
  color: #569cd6;

  font-size: 12px;
  font-weight: 500;

  white-space: nowrap;
}

/* hello */
.group-value {
  font-family: var(--mono);
  font-size: 13px;

  color: #ce9178;

  white-space: nowrap;
}
</style>
