# Todo List 小專案教學

這是一個使用 Vue 3 `<script setup>` 語法實作的簡單代辦清單系統。

主要目的是教學與分享邏輯思維，搭配註解與補充說明，讓初學者能理解每段程式背後的設計原因與使用方式。

---

## 功能說明

- 新增代辦事項
- 刪除代辦事項
- 標記完成（使用 checkbox 與刪除線效果）
- 空清單提示

---

## 技術亮點

- 使用 `ref` 處理響應式資料
- 使用 `.trim` 清理輸入空白
- 使用 `filter` 處理不可變陣列（取代 splice）
- 利用 template literal 產生對應 ID，搭配 `label for` 提升可用性

---

## 延伸補充

- `ref` 與 `reactive` 差異
- `filter` vs `splice` 副作用比較
- Arrow Function 語法概念與傳統 function 差異
- [`MDN：filter`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/filter)  
- [`MDN：箭頭函數`](https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/Arrow_functions)

---

## 進階概念提醒（給未來的你）

如果你已經開始處理更複雜的資料操作，或未來打算封裝元件、管理陣列內的物件狀態，  
這些主題會非常重要：

- 記憶體參考與物件共享（`ref` 裡裝的是什麼？）
- reactive 陣列與物件的 shallow copy / deep copy 問題
- push 物件 vs 建立新物件 vs 替換值的副作用
- 元件封裝時的 props 傳遞與響應式失效問題

這些主題之後會在其他教學主題中逐一深入。  
現在先知道這些字眼，未來你掉坑時會想起：「啊！我曾經在某個地方看過這些！」

---

## 作者備註

這是我給剛開始學 Vue 的朋友的一份筆記，  
希望這份簡單的小清單能陪你打好基礎、理解程式碼的邏輯與溫度。

如果你已經具備更多 Vue 經驗，可能會發現這裡還有許多可以優化的空間——  
但這份範例的設計重點，**是為了對應一位剛入門的朋友，所精心調整的深度與步調。**

有些更進階的觀念（例如抽出 component、使用 composables、進階狀態管理等）會留待未來的主題中再探討。

如果你也覺得這樣的寫法對你有幫助，歡迎留言、交流或 star。
