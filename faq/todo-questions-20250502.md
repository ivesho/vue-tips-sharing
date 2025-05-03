# Todo 教學補充問答紀錄（2025/05/02）

本篇整理自 PR [#1](https://github.com/ivesho/vue-tips-sharing/pull/1) 中學生提出的問題與註解，  
針對 Vue `<script setup>` 中的函式宣告方式、資料刪除邏輯等進行補充說明。

---

## Q1. 為什麼所有函式都使用 `const` 來宣告？會影響效能嗎？

### 原始提問：
> 我發現您所有函式都用 `const` 宣告，請問這是效能考量嗎？  
> `const` 是否比較耗資源？或只是編碼習慣？

### 解釋：

是**「預設不可變」的設計習慣**。  
使用 `const` 宣告函式，可以防止這個變數被重新指派（例如不小心覆蓋掉函式內容），  
這樣可以讓程式碼更安全，也更容易維護。

效能方面，**現代瀏覽器引擎（如 V8）對 `const/let/var` 已經高度優化**，  
在大多數情況下差異幾乎可以忽略，選擇 `const` 是以**可讀性與安全性優先**的設計考量。

---

## Q2. 為什麼要另外將.push後的函式另外const createNewTodoItem出來寫呢?是為了簡化嗎?還是另有其他目的?(20250502)
```js
todoItems.value.push(createNewTodoItem(val));
...
const createNewTodoItem = (textVal) => ({id:id++, text:textVal, isDone:false});
```

### 解釋：
將它抽出成具名函式有三個好處：

可讀性提升：看到 `createNewTodoItem()` 就知道這是「建立項目」的動作
未來好擴充：你只需改一個地方就能新增欄位（例如 createdAt）
重複使用與測試更方便：其他功能也能共用這段邏輯

這不只是為了簡化，而是為了「封裝」與「責任分離」。

---

## Q3. 為甚麼要特地指定delTodo的id? 如果單純指向物件，也同樣會刪除該項目的話，我不太理解指定成id的用意？
```js
const delTodo = (id) => todoItems.value = todoItems.value.filter(x=>x.id != id);
```

### 解釋：

你的意思是說為甚麼不要直接用這樣嗎？
```js
const delTodo = (item) => todoItems.value = todoItems.value.filter(x=>x != item);
```

因為如果是用物件記憶體位置參考比對的話，
你沒辦法確定方法接收到的`item`是與你真正心中所想的`item`相同，
就會變成玄學
```js
var AItem = {text:'A'};
var A_Item = {text:'A'};
console.log(AItem == BItem);  //結果是false
```
尤其當專案不只一個人在開發時候，盡量保持在用唯一值讓各自的功能去抓取同樣的東西，做期待的事情。
因為像刪除來說，我不需要更改任何內容，
只需要透過唯一值在源頭資料，找到那個`item`，進行處理就好。

另外，直接傳整個物件，會造成多餘記憶體傳遞與 GC 負擔
傳入大型物件或深層結構時，整份資料都要參與運算與保留參考

尤其是在 .filter() 這種會遍歷整個陣列的操作中，
若每次都用物件比對，瀏覽器可能得不斷做 reference 判斷與 GC（垃圾回收）

這種行為雖不至於導致明顯卡頓，但在大型資料集或頻繁操作下會逐漸累積性能壓力。

所以唯一值（如 id）是一種最小資料單位、最佳定位方式
id 是 原始資料類型(Primitive Data Types) (數值/字串)，比對成本低
不需要保留原物件參考，可自由重建或轉換資料結構

這也是為什麼資料庫、API、與 UI 元件大多會使用唯一值來操作資料的根本原因。
延伸教學：在資料庫裡會提到主鍵(Primary Key，PK)。
