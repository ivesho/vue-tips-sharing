<script setup>
import { ref, onMounted } from 'vue'

const stations = ref([])
const isLoading = ref(true)
const error = ref(null)

onMounted(async () => {
  try {
    const res = await fetch('https://tcgbusfs.blob.core.windows.net/dotapp/youbike/v2/youbike_immediate.json')
    if (!res.ok) throw new Error(`HTTP 錯誤：${res.status}`)
    const data = await res.json()
    console.log(data);  // 瀏覽器按下F12 透過主控台console頁籤確認
    stations.value = data
  } catch (err) {
    error.value = err.message
  } finally {
    isLoading.value = false
  }
})
</script>

<template>
  <div class="container">
    <h1>YouBike 即時站點資料（前 10 筆）</h1>

    <div v-if="isLoading" class="status">...資料載入中...</div>
    <div v-else-if="error" class="status error">╳ {{ error }}</div>
    
    <table v-else>
      <thead>
        <tr>
          <th>站名</th>
          <th>行政區</th>
          <th>站點地址</th>
          <th>可借車輛</th>
          <th>可還車位</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="station in stations.slice(0, 10)" :key="station.sno">
          <td>{{ station.sna }}</td>
          <td>{{ station.sarea }}</td>
          <td>{{ station.ar }}</td>
          <td>{{ station.available_rent_bikes }}</td>
          <td>{{ station.available_return_bikes }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<style scoped>
.container {
  max-width: 720px;
  margin: 2rem auto;
  padding: 1rem;
  font-family: system-ui, sans-serif;
  background: #fdfdfd;
  border-radius: 8px;
  box-shadow: 0 2px 8px rgba(0,0,0,0.05);
}

h1 {
  font-size: 1.5rem;
  margin-bottom: 1rem;
  text-align: center;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background: #f0f0f0;
}

th, td {
  text-align: left;
  padding: 0.75rem;
  border-bottom: 1px solid #ddd;
}

.status {
  text-align: center;
  padding: 1rem;
  font-size: 1.1rem;
}

.status.error {
  color: darkred;
}
</style>
