# ★ MessageBoard + Modal 教學總覽

本教案目的是解決初學者在學習 Vue 3 中常見的三大困境：

1. **會寫語法，但不知道為什麼要這樣寫**（缺乏設計邏輯）
2. **混淆 props / emit / slot 的使用場景與本質差異**
3. **在元件封裝與拆解時沒有方向，只會把程式寫在一個大檔案中**

透過這份留言板＋彈窗元件的教學，我們將帶領你從「能寫」進入「會設計」。

---

## ◎ 教學目標

- 建立 **元件封裝思維**
- 了解並能區分 **props / emit / slot** 的責任邊界
- 掌握 Modal 的開關控制與 slot 客製技巧
- 培養資料驅動與視覺控制分離的架構邏輯

---

## ◎ 教學情境說明

我們設計了一個留言板系統：

- 點擊「查看詳情」會跳出彈窗顯示完整留言資訊（右上角關閉 X 被隱藏）
- 點擊「新增留言」會跳出另一個彈窗表單，輸入留言內容後可送出（自訂 footer 區塊）

---

## ◎ 元件拆解設計

### Modal.vue

- 接收父層 props：`visible`, `showCloseX`, `closeOnBackdropClick`
- 使用 slot 提供彈性視覺區塊：`header`, `default`, `footer`
- 使用 emit 發送 `close` 事件，交由父層控制狀態（維持單向資料流）
- 預設 footer 內含關閉按鈕，也可透過 slot 覆蓋
- 點擊 backdrop 區域使用 `@click.self` 控制關閉，避免誤觸 modal 本體

### MessageBoard.vue

- 使用 `v-model` 綁定使用者輸入與畫面狀態
- 顯示留言清單，使用 `v-for` 動態渲染
- 彈窗顯示與資料傳遞透過 props / emit 控制
- 新增留言的 Modal 拆成兩個 slot 區塊：
  - `slot:default` 放入 `<form>` 結構
  - `slot:footer` 放入 `<button type="submit">`
  - 因 slot 被拆開，為避免綁死結構，使用 `form="newMsgModalHeader"` 對應 form id，實現跨 slot 提交（HTML 原生寫法）

---

## ◎ 補充知識點整理

- √ **組件封裝觀念**：將 Modal 拆出能提升重用性與可維護性，避免每頁都重複編寫樣板與邏輯
- √ **slot 可預設又可覆蓋**：footer slot 提供預設按鈕，父層可自由覆蓋成送出、刪除等行為按鈕
- √ **v-if vs v-show 的使用選擇**：本範例使用 `v-if`，確保每次開啟時都是「乾淨」的初始狀態（例如表單清空）
- √ **props + emit 邏輯分離**：父元件為狀態主控，子元件僅通知事件，不直接改變可見狀態（維持資料流單向）
- √ **@click.self 背景關閉控制**：避免點擊 Modal 本體造成誤關，僅背景區塊可觸發關閉事件
- √ **slot 跨結構行為控制技巧**：使用 `form="id"` 實現跨 slot 的表單提交行為，保持彈性與 HTML 語意

---

## ◎ 建議延伸挑戰

- ▲ 加上 `width` props 控制 Modal 尺寸大小
- ▲ 留言資料改為透過 API 或 json 載入
- ▲ 增加「編輯留言」情境，支援帶入初始值的表單重用

---

## ◎ 教學理念

這份教材的重點不在於「會寫出彈窗」，而是讓你從架構中理解元件設計的邏輯，知道：

- 什麼是設計思維
- 怎麼區分責任與結構
- 為什麼這樣寫才能讓程式越寫越穩

因為功能可以寫出來，但「設計觀念」必須靠理解與實踐累積。

