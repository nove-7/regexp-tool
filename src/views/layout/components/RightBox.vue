<script setup>
import { Grid, List, Switch } from '@element-plus/icons-vue'
import Preview from './Preview.vue'
import MatchList from './MatchList.vue'
import ReplaceResult from './ReplaceResult.vue'

const activeIndex = defineModel('activeIndex')
const data = defineModel()
const handleSelect = (key) => {
  activeIndex.value = key
}
</script>
<template>
  <div class="box right-box">
    <!-- 常用正则 -->
    <!-- <el-form-item>
      <el-text size="large">常用：</el-text>
      <el-dropdown v-for="list in regexPresets" :key="list.category" @command="handleCommand">
        <el-button type="info">
          {{ list.category }}<el-icon class="el-icon--right"><arrow-down /></el-icon>
        </el-button>
        <template #dropdown>
          <el-dropdown-menu>
            <el-dropdown-item v-for="item in list.list" :key="item" :command="item">{{
              item.label
            }}</el-dropdown-item>
          </el-dropdown-menu>
        </template>
      </el-dropdown>
    </el-form-item>
    <el-divider /> -->
    <!-- 上侧数量 -->
    <div class="result-count">
      <el-text class="string-length" type="info">字符串长度：{{ data.strLength }}</el-text>
      <el-divider direction="vertical" border-style="dashed" />
      <el-text class="match-count" type="success">匹配数量：{{ data.matchCount }}</el-text>
      <el-divider direction="vertical" border-style="dashed" />
      <el-text class="capture-count" type="primary">捕获组数：{{ data.groupCount }}</el-text>
    </div>
    <!-- 菜单栏 -->
    <el-menu
      :key="activeIndex"
      :default-active="activeIndex"
      class="el-menu"
      mode="horizontal"
      background-color="#e5e5e5"
      text-color="#6C6E72"
      active-text-color="#141414"
      @select="handleSelect"
      height="50px"
    >
      <el-menu-item index="1">
        <el-icon><Grid /></el-icon>
        <template #title>匹配结果</template>
      </el-menu-item>
      <el-menu-item index="2">
        <el-icon><List /></el-icon>
        <template #title>匹配列表</template>
      </el-menu-item>
      <el-menu-item index="3">
        <el-icon><Switch /></el-icon>
        <template #title>替换结果</template>
      </el-menu-item>
    </el-menu>
    <!-- 结果 -->
    <div class="content">
      <Preview
        v-if="activeIndex === '1'"
        :matchHighlight="data.matchHighlight"
        :textareaContent="data.textareaContent"
      />

      <MatchList
        v-else-if="activeIndex === '2'"
        :matchResultIndex="data.matchResultIndex"
        :matchHighlight="data.matchHighlight"
        :groupHighlight="data.groupHighlight"
      />

      <ReplaceResult v-else-if="activeIndex === '3'" :replaceResult="data.replaceResult" />
    </div>
  </div>
</template>
<style scoped>
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
/* 右侧 */
.right-box {
  flex: 6;

  display: flex;
  flex-direction: column;

  height: 100%;
  min-height: 0;
}
/* 常用正则下拉框 */
/* .el-dropdown {
  margin-left: 10px;
} */
/* 标题 */
.box-title {
  font-size: 18px;
  font-weight: 600;
  color: #f3f4f6;
  margin-bottom: 20px;
}
/* 上侧数量 */
.result-count {
  padding: 0 0 10px 0;
}
.string-length,
.capture-count,
.match-count {
  padding: 0 20px;
}
.el-menu {
  height: 45px;
  border-radius: 18px;
  overflow: hidden;
}
/* .content {
  display: flex;
  flex-direction: column;
  margin-top: 10px;
  background: #e5e5e5;
  border: 1px solid #333333;
  border-radius: 18px;
  height: 72%;
  overflow: hidden;
} */
.content {
  flex: 1;

  display: flex;
  flex-direction: column;

  margin-top: 10px;

  background: #e5e5e5;
  border: 1px solid #333333;
  border-radius: 18px;

  overflow: hidden;

  min-height: 0;
}
</style>
