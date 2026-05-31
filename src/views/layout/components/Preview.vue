<script setup>
import { computed } from 'vue'
defineOptions({
  name: 'PreviewComponent',
})
const props = defineProps({
  textareaContent: {
    type: String,
    default: '',
  },
  matchHighlight: {
    type: String,
    default: '',
  },
})

// 这种思想是存储字符串，还有一种存储index，这样就既可以有index也能拿到字符串
const tokens = computed(() => {
  const text = props.textareaContent || ''
  const keywords = props.matchHighlight || []

  // 没有关键词，直接返回整段文本
  if (!keywords.length) {
    return [{ text, highlight: false }]
  }

  // 把关键词拼成正则（a|b|c）
  const reg = new RegExp(keywords.join('|'), 'g')

  const result = []

  let lastIndex = 0

  /**
   * replace 的高级用法：
   * 第二个参数函数可以拿到 match + 位置
   */
  text.replace(reg, (match, offset) => {
    // =========================
    // 1️ 添加“匹配前的普通文本”
    // =========================
    if (offset > lastIndex) {
      result.push({
        text: text.slice(lastIndex, offset),
        highlight: false,
      })
    }

    // =========================
    // 2️ 添加“高亮文本”
    // =========================
    result.push({
      text: match,
      highlight: true,
    })

    // =========================
    // 3️ 更新游标位置
    // =========================
    lastIndex = offset + match.length
  })

  // =========================
  // 4️ 处理最后剩余文本（可能最后一个匹配结果后面还有内容）
  // =========================
  if (lastIndex < text.length) {
    result.push({
      text: text.slice(lastIndex),
      highlight: false,
    })
  }

  return result
})
</script>
<template>
  <div class="preview-container">
    <div class="preview-content">
      <span
        v-for="(token, index) in tokens"
        :key="token.text + index"
        :class="{ highlight: token.highlight }"
      >
        {{ token.text }}
      </span>
    </div>
  </div>
</template>
<style scoped>
.preview-container {
  flex: 1;
  overflow: hidden;
  padding: 16px;
}

.preview-content {
  width: 100%;
  height: 100%;

  overflow-y: auto;

  white-space: pre-wrap;
  word-break: break-word;

  line-height: 1.8;

  color: #1e1e1e;

  font-family: 'JetBrains Mono', 'Fira Code', 'Cascadia Code', monospace;

  font-size: 15px;
}
:deep(.highlight) {
  /* background: #fdf0d0; */
  background: #e0e9fd;
  /* background: #0d6efd14; */

  color: #1e1e1e;

  border-radius: 4px;
  /* border: #f5c443 solid 1px; */
  border: #2c5fcd solid 1px;
  padding: 1px 2px;

  font-weight: 600;
}
</style>
