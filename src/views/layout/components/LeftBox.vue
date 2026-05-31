<script setup>
import { computed, ref, watchEffect } from 'vue'
import { Search, Switch } from '@element-plus/icons-vue'
import { useStorage } from '@vueuse/core'

/* 
测试
(\d+)-(\d+)
bbb2025-05 2026-06 aaa

*/
const activeIndex = defineModel('activeIndex')
// 修饰符
let flags = useStorage('flags', [])
// 生成的修饰符按顺序排序 - 计算属性
const flagString = computed(() => {
  const order = ['g', 'i', 'm', 's', 'u']
  return order.filter((item) => flags.value.includes(item)).join('')
})
// 正则表达式（input输入框）
let reg = useStorage('reg', '')
//正则表达式并且包含修饰符
const regex = computed(() => {
  try {
    return new RegExp(reg.value, flagString.value) // 去掉前面的斜杠
  } catch (e) {
    console.log(e)
    return null // 如果正则表达式无效，返回null
  }
})

// 测试文本(input富文本框内容)
let textareaContent = useStorage('textareaContent', '')
// 替换的文本
let replaceText = ref('')

// 匹配结果
const matchResult = computed(() => {
  if (regex.value && textareaContent.value) {
    // 进行匹配,对有无全局匹配使用不同函数
    if (flags.value.includes('g')) {
      return [...textareaContent.value.matchAll(regex.value)]
    } else {
      return [textareaContent.value.match(regex.value)]
    }
  } else {
    return []
  }
})
// 匹配结果索引
// const matchResultIndex = computed(() => {
//   if (regex.value && textareaContent.value) {
//     // 进行匹配,对有无全局匹配使用不同函数
//     if (flags.value.includes('g')) {
//       return [...textareaContent.value.matchAll(regex.value)].map(
//         (item) => item.index + '-' + (item.index + item[0].length - 1),
//       )
//     } else {
//       return [
//         textareaContent.value.match(regex.value)?.index +
//           '-' +
//           (textareaContent.value.match(regex.value)?.index +
//             textareaContent.value.match(regex.value)[0]?.length -
//             1),
//       ]
//     }
//   } else {
//     return null
//   }
// })
const matchResultIndex = computed(() => {
  const text = textareaContent.value
  const reg = regex.value

  if (!text || !reg) return []

  // 全局匹配
  if (flags.value.includes('g')) {
    return [...text.matchAll(reg)].map((item) => {
      const start = item.index
      const end = start + item[0].length - 1
      return `${start}-${end}`
    })
  }

  // 非全局匹配
  const match = text.match(reg)

  if (!match || match.index == null) return []

  const start = match.index
  const end = start + match[0].length - 1

  return [`${start}-${end}`]
})
// 替换操作
const replaceResult = ref('')
const handleReplace = () => {
  if (replaceText.value && regex.value && textareaContent.value) {
    replaceResult.value = textareaContent.value.replace(regex.value, replaceText.value)
    activeIndex.value = '3'
  } else {
    replaceResult.value = '暂无匹配结果'
    activeIndex.value = '3'
  }
}
// 替换后的字符串（替换结果）
// const replaceResult = computed(() => {
//   return matchResult.value.replace(regex.value, replaceText.value)
// })

// 在右边盒子展示的数据
// 1.字符串长度
const strLength = computed(() => {
  return textareaContent.value.length
})
// 2.匹配数量
const matchCount = computed(() => {
  // if (flags.value.includes('g')) {
  //   return matchResult.value.length
  // } else {
  //   return 1
  // }

  if (matchResult.value) {
    if (flags.value.includes('g')) {
      return matchResult.value.length
    } else {
      return 1
    }
  } else {
    return 0
  }
})
// 3.捕获组数
// const groupCount = computed(() => {
//   if (flags.value.includes('g')) {
//     return matchResult.value.reduce((sum, item) => {
//       return sum + item.length - 1
//     }, 0)
//   } else {
//     return matchResult.value[0].length - 1
//   }
// })
const groupCount = computed(() => {
  const list = matchResult.value

  if (!Array.isArray(list) || list.length === 0) return 0

  // g 模式：多个匹配
  if (flags.value.includes('g')) {
    return list.reduce((sum, item) => {
      if (!item || !item.length) return sum
      return sum + (item.length - 1)
    }, 0)
  }

  // 非 g 模式：单个匹配
  const first = list[0]
  if (!first || !first.length) return 0

  return first.length - 1
})
// 高亮的字符串
// 1.匹配数高亮字符串
// const matchHighlight = computed(() => {
//   if (flags.value.includes('g')) {
//     let result = []
//     matchResult.value.forEach((item) => {
//       result.push(item[0])
//     })
//     return result
//   } else {
//     return [matchResult.value[0][0]]
//   }
// })
const matchHighlight = computed(() => {
  const list = matchResult.value ?? []

  if (!Array.isArray(list) || list.length === 0) return []

  if (flags.value.includes('g')) {
    return list.filter((item) => item && item[0]).map((item) => item[0])
  }

  const first = list[0]
  if (!first || !first[0]) return []

  return [first[0]]
})
// 2.元组数高亮字符串
// const groupHighlight = computed(() => {
//   if (!flags.value || !matchResult.value) return []
//   if (flags.value.includes('g')) {
//     let result = []
//     matchResult.value.forEach((item) => {
//       result.push(item.slice(1, item.length))
//     })
//     return result
//   } else {
//     return [matchResult.value[0].slice(1, matchResult.value[0].length)]
//   }
// })
const groupHighlight = computed(() => {
  const list = matchResult.value ?? []

  if (!Array.isArray(list) || list.length === 0) return []

  if (flags.value.includes('g')) {
    return list
      .filter((item) => typeof item === 'string' && item.length > 0)
      .map((item) => item.slice(1))
  }

  const first = list[0]
  if (typeof first !== 'string') return []

  return [first.slice(1)]
})
/* 
需要传出
textareaContent测试文本
matchResult匹配结果
matchResultIndex匹配结果索引
strLength字符串长度
matchCount匹配数量
groupCount捕获组数
matchHighlight高亮的字符串
groupHighlight高亮的元组
replaceResul替换结果
*/
const data = defineModel()
watchEffect(() => {
  data.value = {
    textareaContent: textareaContent.value,
    matchResult: matchResult.value,
    matchResultIndex: matchResultIndex.value,
    strLength: strLength.value,
    matchCount: matchCount.value,
    groupCount: groupCount.value,
    matchHighlight: matchHighlight.value,
    groupHighlight: groupHighlight.value,
    replaceResult: replaceResult.value,
  }
})
</script>
<template>
  <div class="box left-box">
    <div class="box-title">正则表达式测试工具</div>
    <!-- input输入框 -->
    <el-input v-model="reg" style="max-width: 600px" placeholder="请输入正则表达式">
      <template #prepend>/</template>
      <template #append>/{{ flagString }}</template>
    </el-input>
    <!-- 修饰符多选框 -->
    <el-checkbox-group v-model="flags">
      <el-text>修饰符：</el-text>
      <el-checkbox value="g" label="g/全局" />
      <el-checkbox value="i" label="i/忽略大小写" />
      <el-checkbox value="m" label="m/多行" />
      <el-checkbox value="s" label="s/含换行" />
      <el-checkbox value="u" label="u/Unicode" />
    </el-checkbox-group>
    <!-- 富文本框（输入需要测试的内容） -->
    <el-form-item label="">
      <el-text size="large">测试文本</el-text>
      <el-input v-model="textareaContent" type="textarea" :rows="22" resize="none" />
    </el-form-item>
    <!-- 替换的输入框 -->
    <div class="mt-4">
      <el-input
        v-model="replaceText"
        style="max-width: 600px"
        placeholder="请输入需要替换的文本"
        class="input-with-select"
      >
        <template #prepend>
          <el-button :icon="Switch">替换为：</el-button>
          <!-- <el-button :icon="Switch" /> -->
          <!-- <el-text>替换为：</el-text> -->
          <!-- <el-icon><Switch /></el-icon> -->
        </template>
        <template #append>
          <el-button :icon="Search" @click="handleReplace" />
        </template>
      </el-input>
    </div>
    <!-- <div class="test"></div> -->
  </div>
</template>
<style scoped>
/* 左侧 */
.box {
  background: #252526;
  border: 1px solid #333333;
  border-radius: 18px;
  padding: 20px;
  box-sizing: border-box;
  box-shadow:
    0 0 0 1px rgba(255, 255, 255, 0.03),
    0 8px 30px rgba(0, 0, 0, 0.35);
  overflow: hidden;
}
.left-box {
  /* width: 580px; */
  flex: 4;
}
/* 标题 */
.box-title {
  font-size: 18px;
  font-weight: 600;
  color: #f3f4f6;
  margin-bottom: 20px;
}

/* 富文本框（输入需要测试的内容） */
:deep(.el-textarea) {
  --el-input-bg-color: #e5e5e5;
}
:deep(.el-textarea__inner) {
  font-family: Consolas, Monaco, 'Courier New', monospace;

  font-size: 14px;
  line-height: 1.6;
}
.test {
  width: 100px;
  height: 100px;
  /* background: #2d2d2d; */
  /* background: #3c3c3c; */
  /* background: #858585; */
  background: #e5e5e5;
}
</style>
