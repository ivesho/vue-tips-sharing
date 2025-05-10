
# Vue 監聽中的 `{ deep: true }` 說明與 `ref` vs `reactive` 差異

---

## Q1：請問這個 `{ deep: true }` 是因為接下來使用者的操作都會進行深拷貝，所以才要監聽嗎？

不是的，`{ deep: true }` 並不是「因為我們會進行深拷貝」而要加，而是因為 Vue 預設的 `watch()` **只會追蹤頂層屬性的變化**，若我們要追蹤物件中的**巢狀屬性**，才需要加上  `{ deep: true }` 。
因為我們要追蹤，長度、大寫、小寫、數字與符號。它們有變動我就得檢查與長度之間是否不符，但我這樣寫法其實不好，算是有點偷懶。建議未來你可以採用精準追蹤，如下：

```js
// 精準監聽欄位變化
watch(
  () => [form.value.length, form.value.upper, form.value.lower, form.value.symbol, form.value.number],
  () => {
    console.log('form 欄位變化了！可以更新狀態或提示使用者')
  }
)
```

---

### 1. 使用 `ref()` 包裝物件時的監聽行為

Vue 中的 `ref()` 可用來包裝任何值（基本型別或物件）。當我們使用 `ref()` 包裝物件時，Vue 並不會遞迴地讓巢狀屬性變成響應式，這就造成了下列差異：

#### 範例

```js
const data = ref({
  len: 12,
  info: {
    title: '我是一個標題'
  }
})
```

```js
watch(data, () => {
  console.log('√ deep watch 被觸發')
}, { deep: true })
```

#### √ 會觸發 watch 的情況：

- `data.value.len = 20`
- `data.value.info.title = '新標題'`
- `data.value.info = { title: '整個替換 info' }`
- `data.value = { len: 10, info: { title: '整個替換 data' } }`

#### 不加 `{ deep: true }` 時，只會觸發以下：

- `data.value = {...}`（整個物件被替換）

巢狀屬性（如 `.info.title`）的變動則完全不會觸發。

---

### 2. 使用 `reactive()` 的監聽行為 (會隱性的創建一個深層監聽器)

Vue 的 `reactive()` 本身就會將整個物件及其所有巢狀屬性做成**遞迴式的響應式物件**，這表示：

```js
const state = reactive({
  len: 12,
  info: {
    title: '我是一個標題'
  }
})

watch(state, () => {
  console.log('√ reactive watch 被觸發')
})
```

#### √ 以下情況都會觸發：

- `state.len = 20`
- `state.info.title = '改變囉'`
- `state.info = { title: '整個替換' }`

**不需要 `{ deep: true }`！**

---

### 3. ref vs reactive 的對照說明

| 特性比較             | `ref({})`                     | `reactive({})`               |
|----------------------|-------------------------------|-----------------------------|
| 包裝類型             | 單一變數（.value）             | 直接物件（展開所有層級）       |
| 是否自動深層追蹤      | 否                             | 是                          |
| watch 需加 `{ deep: true }` | 需要                   | 不需要                       |
| 是否能整包 reset      | √ `.value = {...}`           | X 需用 `Object.assign()`       |
| 適合用於             | 基本型別、單一值的響應追蹤       | 多層物件、表單、設定檔等       |

---

### [Vue官方 深層追蹤](https://cn.vuejs.org/guide/essentials/watchers.html#deep-watchers)

---

## Q2：這樣監聽的結果會返還資料回來嗎? 我們會需要這筆資料接續著進行什麼操作嗎？

watch 一定會返回，新值與舊值，如官方教學，同樣是在深層追蹤篇：

```js
const obj = reactive({ count: 0 })

watch(obj, (newValue, oldValue) => {
  // 注意：`newValue` 此處和 `oldValue` 是相等的，是沒有差異的
  // 因為它們是同一個記憶體參考位置
  /*
    還記得我們之前討論過的，你修改物件裡面的屬性，會導致所有相同參考的變數都受影響。
    let oldValue = {id:1,value:'我是old'}
    let newValue = oldValue
    newValue.value = '我變成new'

    console.log(oldValue)
    console.log(newValue)
    //你會發現它們一模一樣
    //watch如果追蹤的是響應式對象，它監聽提供給你的new 與 old 會試一模一樣的。
  */
})

obj.count++
```

因為，一模一樣，所以我在範例中並沒有採用它給的new 與 old，而是直接去找form 變數拿。

---

## Q3：請問這個 `generatePassword()` 中的 `alert` 是什麼時候會啟用呢？

確實，在目前設計下 `generatePassword` 按鈕已經會被 disable 掉，所以透過介面去操作是不可能觸發產生密碼的。
但無法保證未來功能擴充時，其他開發者是否會在程式中直接呼叫 `generatePassword()`。
如果缺乏第二層檢查，這可能導致邏輯錯誤或安全問題。
所以這裡是二次檢查(其實二次檢查最保險是去檢查長度與其他規則的數量是否和邏輯)，
在這裡我採取了相對簡化的方式，透過 `hint` 作為安全檢查依據。只要變更來源沒有繞過 `watch` 的追蹤邏輯，我們就可以合理相信 `hint` 的值是可信的。
```js
const generatePassword = () => {
  const { length, upper, lower, symbol, number } = form.value;
  const minRequired = upper + lower + symbol + number;

  if (hint.value) {
    alert(`密碼設定長度 ${length}，但規則需求長度 ${minRequired}。請重新確認。`);
    return;
  }

  ........

}
```
