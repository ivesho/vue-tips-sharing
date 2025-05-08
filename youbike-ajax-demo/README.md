# YouBike AJAX Demo ｜Vue 3 + fetch + Open Data

這是一個使用 Vue 3 撰寫的小型練習專案，從臺北市政府交通局開放資料平台取得 YouBike 2.0 即時資料，並以表格形式顯示前 10 筆站點資訊。

本專案用於教學用途，作為初學者學習 Vue 與 AJAX 的入門範例。

---

## 畫面預覽

> 站點資訊包含站名、行政區、地址、可借車輛與可還空位等欄位。  
> 載入過程會顯示 loading 提示，若發生錯誤則顯示錯誤訊息。

---

## 技術重點

- Vue 3 Composition API (`<script setup>`)
- fetch() 非同步資料請求
- DevTools Console / Network 觀察資料結構
- v-for 與資料綁定
- 簡易錯誤處理與 loading 狀態提示
- Open Data 實務串接（無需金鑰）

---

## 延伸學習（可參考教學說明.md）

- 搜尋與條件篩選
- 自動更新（setInterval）
- localStorage 資料快取
- 分頁 / 捲動顯示等資料處理技巧

詳細內容請參考 `教學說明.md`

---

## 資料來源

資料來源：臺北市政府交通局 YouBike 2.0 即時資料  
📎 https://data.gov.tw/dataset/137993

> 資料每分鐘更新一次，無需註冊金鑰，可直接使用於教學或前端練習。

---

## 📄 License

MIT（教學用途自由使用，請保留資料來源與說明註記）
