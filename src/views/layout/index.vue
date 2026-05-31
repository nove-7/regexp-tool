<script setup>
import { ref } from 'vue'
import LeftBox from './components/LeftBox.vue'
import RightBox from './components/RightBox.vue'
defineOptions({
  name: 'LayoutIndex',
})
const activeIndex = ref('1')

const data = ref({
  textareaContent: '',
  matchResult: [],
  matchResultIndex: [],
  strLength: 0,
  matchCount: 0,
  groupCount: 0,
  matchHighlight: '',
  groupHighlight: '',
  replaceResult: '',
})

const regexLibrary = [
  {
    category: '校验数字的表达式',
    list: [
      { purpose: '数字', regex: String.raw`^\d+$`, example: '12345', desc: '非负整数' },
      { purpose: 'n位数字', regex: String.raw`^\d{n}$`, example: '1234', desc: '固定n位数字' },
      { purpose: '至少n位数字', regex: String.raw`^\d{n,}$`, example: '12345', desc: '至少n位' },
      { purpose: 'm-n位数字', regex: String.raw`^\d{m,n}$`, example: '1234', desc: '范围长度' },
      {
        purpose: '零和非零开头数字',
        regex: String.raw`^(0|[1-9]\d*)$`,
        example: '0 / 123',
        desc: '整数',
      },
      {
        purpose: '非零开头最多两位小数',
        regex: String.raw`^[1-9]\d*(\.\d{1,2})?$`,
        example: '12.34',
        desc: '最多两位小数',
      },
      {
        purpose: '正负数带1-2位小数',
        regex: String.raw`^-?\d+(\.\d{1,2})?$`,
        example: '-12.34',
        desc: '正负小数',
      },
      {
        purpose: '正数/负数/小数',
        regex: String.raw`^[-+]?\d+(\.\d+)?$`,
        example: '+12.3',
        desc: '浮点数',
      },
      {
        purpose: '两位小数正实数',
        regex: String.raw`^\d+(\.\d{2})?$`,
        example: '12.34',
        desc: '两位小数',
      },
      {
        purpose: '1~3位小数正实数',
        regex: String.raw`^\d+(\.\d{1,3})?$`,
        example: '12.345',
        desc: '多位小数',
      },
      { purpose: '非零正整数', regex: String.raw`^[1-9]\d*$`, example: '123', desc: '正整数' },
      { purpose: '非零负整数', regex: String.raw`^-[1-9]\d*$`, example: '-123', desc: '负整数' },
      { purpose: '非负整数', regex: String.raw`^\d+$`, example: '0', desc: '>=0整数' },
      { purpose: '非正整数', regex: String.raw`^(-\d+|0)$`, example: '0', desc: '<=0整数' },
      {
        purpose: '非负浮点数',
        regex: String.raw`^\d+(\.\d+)?$`,
        example: '12.3',
        desc: '>=0浮点数',
      },
      {
        purpose: '非正浮点数',
        regex: String.raw`^(-\d+(\.\d+)?|0+(\.0+)?)$`,
        example: '-12.3',
        desc: '<=0浮点数',
      },
      {
        purpose: '正浮点数',
        regex: String.raw`^(([1-9]\d*)|0)\.\d+$`,
        example: '0.1',
        desc: '>0浮点数',
      },
      {
        purpose: '负浮点数',
        regex: String.raw`^-(([1-9]\d*)|0)\.\d+$`,
        example: '-0.1',
        desc: '<0浮点数',
      },
      {
        purpose: '浮点数',
        regex: String.raw`^-?\d+(\.\d+)?$`,
        example: '-12.3',
        desc: '所有浮点数',
      },
    ],
  },

  {
    category: '校验字符的表达式',
    list: [
      { purpose: '汉字', regex: String.raw`^[\u4e00-\u9fa5]+$`, example: '你好', desc: '纯中文' },
      {
        purpose: '英文和数字',
        regex: String.raw`^[A-Za-z0-9]+$`,
        example: 'abc123',
        desc: '字母数字',
      },
      {
        purpose: '4-40位英文和数字',
        regex: String.raw`^[A-Za-z0-9]{4,40}$`,
        example: 'abc12345',
        desc: '长度限制',
      },
      { purpose: '26个英文字母', regex: String.raw`^[A-Za-z]+$`, example: 'abcXYZ', desc: '字母' },
      { purpose: '大写字母', regex: String.raw`^[A-Z]+$`, example: 'ABC', desc: '大写' },
      { purpose: '小写字母', regex: String.raw`^[a-z]+$`, example: 'abc', desc: '小写' },
      { purpose: '数字字母下划线', regex: String.raw`^\w+$`, example: 'abc_123', desc: '常见账号' },
      {
        purpose: '3-20位数字字母下划线',
        regex: String.raw`^\w{3,20}$`,
        example: 'abc_123',
        desc: '长度账号',
      },
      {
        purpose: '中文英文数字下划线',
        regex: String.raw`^[\u4E00-\u9FA5A-Za-z0-9_]+$`,
        example: '你好abc',
        desc: '混合',
      },
      {
        purpose: '中文英文数字（无下划线）',
        regex: String.raw`^[\u4E00-\u9FA5A-Za-z0-9]+$`,
        example: '你好abc',
        desc: '无下划线',
      },
      {
        purpose: '禁止特殊字符',
        regex: String.raw`^[^%&',;=?$"]+$`,
        example: 'abc123',
        desc: '过滤符号',
      },
      { purpose: '禁止~字符', regex: String.raw`^[^~]+$`, example: 'abc123', desc: '禁止~' },
    ],
  },

  {
    category: '特殊需求表达式',
    list: [
      {
        purpose: 'Email地址',
        regex: String.raw`^[\w.-]+@[\w.-]+\.[a-zA-Z]{2,}$`,
        example: 'test@mail.com',
        desc: '邮箱',
      },
      {
        purpose: '域名',
        regex: String.raw`[a-zA-Z0-9][-a-zA-Z0-9]{0,62}(\.[a-zA-Z0-9][-a-zA-Z0-9]{0,62})+\.?$`,
        example: 'www.example.com',
        desc: '域名',
      },
      {
        purpose: 'URL',
        regex: String.raw`^[a-zA-Z]+://[^\s]+$`,
        example: 'https://abc.com',
        desc: '网址',
      },
      {
        purpose: '手机号码',
        regex: String.raw`^(13[0-9]|14[0-9]|15[0-9]|16[0-9]|17[0-9]|18[0-9]|19[0-9])\d{8}$`,
        example: '13812345678',
        desc: '手机号',
      },
      {
        purpose: '电话号码',
        regex: String.raw`^(\d{3,4}-)?\d{7,8}$`,
        example: '010-12345678',
        desc: '座机',
      },
      {
        purpose: '支持分机号电话',
        regex: String.raw`((\d{11})|((\d{7,8})|(\d{3,4}-\d{7,8})|(\d{3,4}-\d{7,8}-\d{1,4})|(\d{7,8}-\d{1,4})))$`,
        example: '010-12345678-123',
        desc: '分机',
      },
      {
        purpose: '身份证号',
        regex: String.raw`(^\d{15}$)|(^\d{18}$)|(^\d{17}[\dXx]$)`,
        example: '110101199003071234',
        desc: '身份证',
      },
      {
        purpose: '合法账号',
        regex: String.raw`^[a-zA-Z][a-zA-Z0-9_]{4,15}$`,
        example: 'abc_123',
        desc: '账号',
      },
      { purpose: '密码', regex: String.raw`^[a-zA-Z]\w{5,17}$`, example: 'a12345_', desc: '密码' },
      {
        purpose: '强密码（无特殊字符）',
        regex: String.raw`^(?=.*\d)(?=.*[a-z])(?=.*[A-Z])[a-zA-Z0-9]{8,10}$`,
        example: 'Abc12345',
        desc: '强密码',
      },
      {
        purpose: '强密码（可特殊字符）',
        regex: String.raw`^(?=.*\d)(?=.*[a-z])(?=.*[A-Z]).{8,10}$`,
        example: 'Abc12345!',
        desc: '强密码+',
      },
      {
        purpose: 'IPv4地址',
        regex: String.raw`((25[0-5]|2[0-4]\d|1\d\d|[1-9]?\d)(\.(?!$)|$)){4}`,
        example: '192.168.1.1',
        desc: 'IP',
      },
      {
        purpose: '日期',
        regex: String.raw`^\d{4}-\d{1,2}-\d{1,2}$`,
        example: '2025-06-30',
        desc: '日期',
      },
      { purpose: '月份', regex: String.raw`^(0?[1-9]|1[0-2])$`, example: '12', desc: '月份' },
      {
        purpose: '日期天',
        regex: String.raw`^((0?[1-9])|((1|2)[0-9])|30|31)$`,
        example: '31',
        desc: '天',
      },
      { purpose: '金额整数', regex: String.raw`^(0|[1-9][0-9]*)$`, example: '100', desc: '金额' },
      {
        purpose: '金额小数',
        regex: String.raw`^[0-9]+(\.[0-9]{1,2})?$`,
        example: '100.00',
        desc: '金额小数',
      },
      { purpose: '空白行', regex: String.raw`^\s*$`, example: '', desc: '空行' },
      { purpose: 'HTML标签', regex: String.raw`<[^>]+>`, example: '<div>', desc: '标签' },
      { purpose: 'QQ号', regex: String.raw`[1-9][0-9]{4,}`, example: '10000', desc: 'QQ' },
      { purpose: '邮政编码', regex: String.raw`[1-9]\d{5}`, example: '100000', desc: '邮编' },
    ],
  },
]
</script>
<template>
  <div class="layout">
    <el-container>
      <!-- <el-header>Header</el-header> -->
      <el-main class="main">
        <!-- 上侧主要测试区域 -->
        <div class="regex-page">
          <!-- 左侧盒子 -->
          <LeftBox v-model="data" v-model:activeIndex="activeIndex" />
          <!-- 右侧盒子 -->
          <RightBox v-model="data" v-model:activeIndex="activeIndex" />
        </div>
      </el-main>
      <el-footer>
        <div v-for="group in regexLibrary" :key="group.category" class="group-box">
          <!--  大标题 -->
          <h3 class="title">
            {{ group.category }}
          </h3>

          <!-- 表格 -->
          <el-table :data="group.list" border style="margin-bottom: 20px">
            <el-table-column prop="purpose" label="用途" width="160" />

            <el-table-column label="正则">
              <template #default="{ row }">
                <code class="code">{{ row.regex }}</code>
              </template>
            </el-table-column>

            <el-table-column prop="example" label="示例" width="180" />
            <el-table-column prop="desc" label="说明" />
          </el-table>
        </div>
      </el-footer>
    </el-container>
  </div>
</template>
<style scoped>
:deep(.el-main) {
  padding: 0;
}
.regex-page {
  display: flex;
  gap: 20px;
  padding: 20px;
  height: calc(100vh - 60px);
  background: #1e1e1e;
  box-sizing: border-box;
}

/* :deep(.el-footer) {
  text-align: center;
  color: #f3f4f6;
  background: #1e1e1e;
  height: 1000px;
} */
:deep(.el-footer) {
  background: #1e1e1e;
  padding: 20px;
  color: #f3f4f6;

  height: auto;
  min-height: 100vh;
}
.group-box {
  background: #252526;
  border: 1px solid #333;
  border-radius: 10px;
  padding: 12px 14px;
  margin-bottom: 18px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.2);
}

.title {
  font-size: 15px;
  font-weight: 600;
  margin: 6px 0 12px;
  padding-left: 10px;
  border-left: 4px solid #409eff;
  color: #e5e5e5;
  letter-spacing: 0.5px;
}
:deep(.el-table) {
  --el-table-bg-color: #1e1e1e;
  --el-table-tr-bg-color: #1e1e1e;
  --el-table-header-bg-color: #2d2d2d;
  --el-table-border-color: #333;
  color: #d4d4d4;
}
:deep(.el-table th) {
  background: #2d2d2d !important;
  color: #ffffff;
  font-weight: 600;
}
:deep(.el-table tr:hover > td) {
  background: #2a2d2e !important;
}
.code {
  font-family: 'Fira Code', monospace;
  background: #2d2d2d;
  color: #dcdcaa;
  padding: 3px 6px;
  border-radius: 4px;
  font-size: 12px;
  display: inline-block;
  white-space: pre-wrap;
  word-break: break-all;
}
</style>
