<script setup>
import { ref } from 'vue'

const h1Title = ref('主標題');  //如果可能會動態改動，在使用ref或reactive做響應式
const h2Title = '副標題'; //否則靜態宣告就好。

let id = 0;
const newTodo = ref("");
const todoItems = ref([]); 

const addTodo = () => {
  const val = newTodo.value;
  if(!val){
    alert("這個代辦事項是要處理寂寞嗎？(請輸入有效內容)");
    return;
  }
  todoItems.value.push(createNewTodoItem(val));
  
  newTodo.value = "";
}

const createNewTodoItem = (textVal) => ({id:id++, text:textVal, isDone:false});
  //您好，我想請問我有發現您的程式碼都是以const作為起始點，就連設定function也是，請問這樣的設定會有影響效能或是只是一種編碼習慣呢?
  //為何要另外將.push後的函式另外const createNewTodoItem出來寫呢?是為了簡化嗎?還是另有其他目的?(20250502)
  
// 為什麼要用過濾方式來做？
// 如果用刪除的邏輯，會需要先找到該筆項目的索引(index)
// 然後再透過 index 操作 splice，例如：todoItems.value.splice(index, 1);
// 先不談副作用或效能，光就程式邏輯來說，會寫得比較多（比較像人的思維，但不一定適合電腦）
// 延伸：splice 是直接改變原本陣列內容；而 filter 是建立並回傳一個新的陣列
//  建議務必留意，每個方法結果不同可能是「改變原資料」，還是「產生新資料」
//相關技術說明：
//https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/splice
//https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/Array/filter
const delTodo = (id) => todoItems.value = todoItems.value.filter(x=>x.id != id);

//`todoDone-${id}` = 'todoDone-' + id
//文件說明如下：
//https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Template_literals
const getTodoDoneElementId = (id) => `todoDone-${id}`;
</script>

<template>
  <h1>{{ h1Title }}</h1>
  <hr>
  <h2>{{ h2Title }}</h2>
  <!-- 沒特殊需求，多一個trim是好習慣，別讓使用者的空白，變成BUG -->
  <!-- 一併提供給您JS的trim文件 -->
  <!-- https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Global_Objects/String/trim -->
  <label for="newTodo">新的代辦事項：</label> <input id="newTodo" v-model.trim="newTodo"/>
  <button v-on:click="addNewTodo" type="button">Add</button>
  
  <h2>代辦事項清單</h2>
  <ul>
    <li v-for="item of todoItems" :key="item.id" style="display: flex;gap:1rem">
      <div style="flex-grow: 1;">
        <input type="checkbox" :id="getTodoDoneElementId(item.id)" v-model="item.isDone">
        <label :style="{ textDecoration: item.isDone ? 'line-through' : 'none' }" :for="getTodoDoneElementId(item.id)">{{item.text}}</label>
        </div>
      <button v-on:click="delTodo(item.id)" style="width: fit-content;" type="button">X</button>
    </li>
  </ul>
  <div v-if="!todoItems.length">你今天超棒的，沒有任何代辦要處理唷！</div>
</template>

<!--
邏輯說明
功能流程：
1. 新增代辦：
   - 使用者輸入內容按下 Add
   - 透過 `v-model.trim` 移除前後空白
   - 若為空字串則警告並終止
   - 呼叫 `createNewTodoItem(text)` 產生新代辦物件
   - 推入 todoItems 陣列
   - 清空輸入框

2. 刪除代辦：
   - 每筆資料都有對應的 id
   - 呼叫 `delTodo(id)` 時，透過 `.filter` 建立新陣列排除該項目
      - 請問這邊為何要特地指定delTodo的id? 如果單純指向物件，也同樣會刪除該項目的話，我不太理解指定成id的用意

3. 標記完成：
   - 勾選 checkbox 綁定 `item.isDone`
   - 以刪除線方式顯示完成狀態

命名與邏輯設計：
- newTodo：使用者輸入的新項目文字
- todoItems：所有代辦清單
- createNewTodoItem(text)：產生代辦項目（封裝 id、自動設定 isDone）
- id：自動遞增的唯一識別碼
- getTodoDoneElementId(id)：產生 checkbox 對應的 id 與 label 的 for 屬性值，確保無障礙與可點擊範圍

防呆設計：
- 輸入欄位使用 `.trim` 防止空格成為有效輸入
- 新增前有空值檢查（避免非 UI 呼叫也出錯）
- 物件封裝處理避免記憶體參照錯誤或共享
- 刪除使用不可變處理方式（filter）

附註：
- 為了保持簡單與教學清晰，未導入 reactive、computed、watch 等進階概念
- 未來若代辦事項變複雜，你可能會改用reactive({......})方式
  須留意將reactive push進入陣列(Array)
  相關知識點：記憶體參照問題、淺拷貝、深拷貝
- 我使用的是箭頭函數
  詳細資訊可以參考：
  - MDN：箭頭函數 - https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Functions/Arrow_functions
  - MDN：函數聲明 - https://developer.mozilla.org/zh-TW/docs/Web/JavaScript/Reference/Statements/function
-->
