<script setup lang="ts">
import { ref } from 'vue'
import { useMessage } from 'naive-ui'

const now = Date.now()
const tsFormat = ref('ms')
const tsInput = ref(now.toString())
const timeInput = ref(formatDate(new Date(now)))
const message = useMessage()

function formatDate(date: Date) {
  // yyyy-MM-dd HH:mm:ss
  const pad = (n: number) => n.toString().padStart(2, '0')
  return `${date.getFullYear()}-${pad(date.getMonth() + 1)}-${pad(date.getDate())} `
    + `${pad(date.getHours())}:${pad(date.getMinutes())}:${pad(date.getSeconds())}`
}

function parseDate(str: string): Date | null {
  // yyyy-MM-dd HH:mm:ss
  const m = str.match(/^(\d{4})-(\d{2})-(\d{2}) (\d{2}):(\d{2}):(\d{2})$/)
  if (!m)
    return null
  const [_, y, mo, d, h, mi, s] = m
  return new Date(Number(y), Number(mo) - 1, Number(d), Number(h), Number(mi), Number(s))
}

function tsToTime() {
  let ts = Number(tsInput.value)
  if (Number.isNaN(ts)) {
    message.error('请输入有效的时间戳')
    return
  }
  // 转为毫秒
  if (tsFormat.value === 's')
    ts *= 1e3
  else if (tsFormat.value === 'us')
    ts = Math.floor(ts / 1e3)
  else if (tsFormat.value === 'ns')
    ts = Math.floor(ts / 1e6)
  const date = new Date(ts)
  if (Number.isNaN(date.getTime())) {
    message.error('无效的时间戳')
    return
  }
  timeInput.value = formatDate(date)
}

function timeToTs() {
  const date = parseDate(timeInput.value)
  if (!date) {
    message.error('请输入有效的时间格式 (yyyy-MM-dd HH:mm:ss)')
    return
  }
  let ts = date.getTime()
  if (tsFormat.value === 's')
    ts = Math.floor(ts / 1e3)
  else if (tsFormat.value === 'us')
    ts = ts * 1e3
  else if (tsFormat.value === 'ns')
    ts = ts * 1e6
  tsInput.value = ts.toString()
}

const formats = [
  { label: '秒 (s)', value: 's' },
  { label: '毫秒 (ms)', value: 'ms' },
  { label: '微秒 (μs)', value: 'us' },
  { label: '纳秒 (ns)', value: 'ns' },
]
</script>

<template>
  <div class="mx-auto max-w-xl flex flex-col gap-6">
    <n-card title="时间戳与时间互转">
      <div class="flex flex-col gap-4">
        <div class="flex items-center gap-2">
          <n-input
            v-model:value="tsInput"
            type="text"
            placeholder="请输入时间戳"
            @keydown.enter="tsToTime"
          />
          <n-select
            v-model:value="tsFormat"
            :options="formats"
            style="width: 120px"
          />
          <n-button type="primary" @click="tsToTime">
            转为时间
          </n-button>
        </div>
        <div class="flex items-center gap-2">
          <n-input
            v-model:value="timeInput"
            type="text"
            placeholder="请输入时间 (yyyy-MM-dd HH:mm:ss)"
            @keydown.enter="timeToTs"
          />
          <n-select
            v-model:value="tsFormat"
            :options="formats"
            style="width: 120px"
            disabled
          />
          <n-button type="primary" @click="timeToTs">
            转为时间戳
          </n-button>
        </div>
      </div>
    </n-card>
  </div>
</template>

<route lang="json">
{
  "meta": {
    "menu": "Timestamp",
    "index": 4
  }
}
</route>
