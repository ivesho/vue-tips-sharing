<template>
  <div class="wrapper">
    <h2>☀️ 早晨任務模擬器：同步 vs 非同步</h2>
    <button @click="startBoth">▶️ 開始模擬</button>

    <div class="log-container">
      <!-- 同步版 -->
      <div class="log-box">
        <h3>同步版（一步一步來）</h3>
        <div class="log-scroll" ref="syncLogBox">
          <ul>
            <li v-for="log of logsSync" :key="'sync-' + id++">{{ log }}</li>
          </ul>
        </div>
      </div>

      <!-- 非同步版 -->
      <div class="log-box">
        <h3>非同步版（平行啟動）</h3>
        <div class="log-scroll" ref="asyncLogBox">
          <ul>
            <li v-for="log of logsAsync" :key="'async-' + id++">{{ log }}</li>
          </ul>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, nextTick } from 'vue';
let id = 0;
const logsSync = ref([]);
const logsAsync = ref([]);
const startTimeSync = ref(0);
const startTimeAsync = ref(0);

// Ref for scroll box
const syncLogBox = ref(null);
const asyncLogBox = ref(null);

// 工具：記錄訊息與時間（模擬時間：250ms = 1分鐘）
const logMessage = (list, start, message, scrollRef) => {
  const now = Date.now();
  const elapsed = ((now - start.value) / 250).toFixed(1);
  list.value.push(`【+${elapsed}分】${message}`);

  nextTick(() => {
    if (scrollRef.value) {
      scrollRef.value.scrollTop = scrollRef.value.scrollHeight;
    }
  });
}

const delay = (ms, message, list, start, scrollRef) => {
  return new Promise(resolve => {
    logMessage(list, start, `開始：${message}`, scrollRef);
    setTimeout(() => {
      logMessage(list, start, `完成：${message}`, scrollRef);
      resolve();
    }, ms);
  });
}

// 同步任務
const runSyncVersion=  async () =>  {
  startTimeSync.value = Date.now();
  logsSync.value = [];
  await delay(2000, '煮咖啡 ☕', logsSync, startTimeSync, syncLogBox);
  await delay(800, '煎蛋（前期）🍳', logsSync, startTimeSync, syncLogBox);
  await delay(1200, '煎蛋（回鍋翻面）🔥', logsSync, startTimeSync, syncLogBox);
  await delay(500, '添加貓咪飼料 🐱', logsSync, startTimeSync, syncLogBox);
  await delay(800, '吃早餐 🍽️', logsSync, startTimeSync, syncLogBox);
  await delay(2500, '丟餐具進洗碗機 🧼', logsSync, startTimeSync, syncLogBox);
  await delay(1000, '換衣服 👕', logsSync, startTimeSync, syncLogBox);
  await delay(600, '收起餐具 🧺', logsSync, startTimeSync, syncLogBox);
  await delay(500, '出門上班 🚌', logsSync, startTimeSync, syncLogBox);
}

// 非同步任務
const runAsyncVersion = async () =>{
  startTimeAsync.value = Date.now();
  logsAsync.value = [];
  const coffee = delay(2000, '煮咖啡 ☕', logsAsync, startTimeAsync, asyncLogBox);
  const eggStart = delay(800, '煎蛋（前期）🍳', logsAsync, startTimeAsync, asyncLogBox);
  await delay(500, '添加貓咪飼料 🐱', logsAsync, startTimeAsync, asyncLogBox);
  await eggStart;
  const eggEnd = await delay(1200, '煎蛋（回鍋翻面）🔥', logsAsync, startTimeAsync, asyncLogBox);
  await Promise.all([coffee, eggEnd]);
  await delay(800, '吃早餐 🍽️', logsAsync, startTimeAsync, asyncLogBox);
  const wash = delay(2500, '丟餐具進洗碗機 🧼', logsAsync, startTimeAsync, asyncLogBox);
  await delay(1000, '換衣服 👕', logsAsync, startTimeAsync, asyncLogBox);
  await wash;
  await delay(600, '收起餐具 🧺', logsAsync, startTimeAsync, asyncLogBox);
  await delay(500, '出門上班 🚌', logsAsync, startTimeAsync, asyncLogBox);
}

const startBoth = () =>{
  runSyncVersion();
  runAsyncVersion();
}
</script>

<style scoped>
.wrapper {
  max-width: 960px;
  margin: auto;
  padding: 24px;
  font-family: sans-serif;
  background: #f7f7f7;
}

button {
  padding: 8px 16px;
  margin-bottom: 20px;
  background-color: #2563eb;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.log-container {
  display: flex;
  justify-content: space-between;
  gap: 20px;
}

.log-box {
  flex: 1;
  border: 1px solid #ccc;
  border-radius: 6px;
  padding: 12px;
  background-color: #fff;
}

.log-scroll {
  max-height: 400px;
  overflow-y: auto;
  background-color: #f9f9f9;
  padding: 8px;
  border-radius: 4px;
}

.log-box h3 {
  margin-top: 0;
  margin-bottom: 8px;
  color: #333;
  font-size: 16px;
}
</style>
