<script setup>
import { ref } from 'vue'
import Modal from './Modal.vue'

/*
  ＃詳解標記區（知識點統整）
  ● 本頁示範如何在父層搭配封裝過的 Modal 元件進行實際應用（顯示／新增兩種情境）。
  ● Modal 內容透過 slot 傳遞，因此可自由定義 header、default、footer，實現視覺與行為彈性。
  ● 查看詳情 Modal 使用 v-if 顯示，並隱藏右上 X（示範 props 控制 + 單向資料流）。
  ● 新增留言 Modal：
    ○ 覆蓋 slot:footer 改成自定義的需求，讓元件使用更加彈性
    ○ 我們在 slot:default 放入了form，slot:footer 放入了提交button，因為提交button沒有被放在form當中，被拆成兩個區塊(slot)放置，為避免結構綁死，使用 form="xxx" 指定 form id，實現跨 slot 提交。
  ● 使用者輸入內容由 v-model 直接綁定，並透過 HTML 原生的 required 驗證表單完整性。
*/

const messages = ref([
  { id: 1, author: "小明", content: "Vue真的好難喔，感覺每個功能都像咒語一樣...", time: "2025-05-13 14:32" },
  { id: 2, author: "小美", content: "我昨天學會了props了耶！超有成就感！", time: "2025-05-14 09:15" },
  { id: 3, author: "小強", content: "slot和emit傻傻分不清楚 QAQ", time: "2025-05-15 18:47" },
  { id: 4, author: "Lumi", content: "其實你們都學得很好了，只是還沒發現自己懂得比想像中多。", time: "2025-05-15 21:10" }
])

const showDetailModal = ref(false)
const selectedMessage = ref(null)

const openDetailModal = (msg_id) => {
  selectedMessage.value = messages.value.find(x=>x.id == msg_id);
  if(!selectedMessage.value){
    alert("這個留言，很像不存在欸？");
    return;
  }
  //請問，上方這個設定是假設錯誤發生時會出現的內容嗎?
  //因為如果沒有可以選擇的查看詳情，好像也不會觸擊到一則不存在的留言
  //意思是在有可能會出現錯誤的環節，我是否都會需要先製作一個如果錯誤發生時的顯示狀況?
  showDetailModal.value = true
}

const showNewModal = ref(false)
const newMessage = ref({
  author: '',
  content: ''
})

const openNewMessageModal = () => {
  showNewModal.value = true
  newMessage.value = { author: '', content: '' }
}

const submitNewMessage = () => {
  if (!newMessage.value.author || !newMessage.value.content) {
    alert("請填寫完整留言喔～")
    return
  }
  //我發現使用form的時候, 這個alert並不會出現, 而是出現它預設的樣子
  //是否通常會去改變它的預設狀態, 而不是使用alert?
  const newId = messages.value.length + 1
  messages.value.push({
    id: newId,
    author: newMessage.value.author,
    content: newMessage.value.content,
    time: new Date().toLocaleString('zh-TW', { hour12: false })
  })
  showNewModal.value = false
}

</script>

<template>
  <div>
    <h1>留言板</h1>

    <button @click="openNewMessageModal">新增留言</button>

    <ul class="message-list">
      <li v-for="{id, author, content } of messages" :key="id" class="message-item">
        <div>
          <strong>{{ author }}</strong> ：{{ content }}
        </div>
        <button @click="openDetailModal(id)">查看詳情</button>
      </li>
    </ul>

    <!-- 查看詳情 Modal -->
    <Modal
      :visible="showDetailModal"
      :showCloseX="false"
      @close="showDetailModal = false" 
    >
      <!--請問@close="showDetailModal = false"這個是按到誰的時候會關閉? 為什麼是將@click設置在這?-->
      <!--我明白子組件有設定的emit, 但我不理解的是@click為什麼不是設置在button-->
      <template #header>
        <h3>留言詳情</h3>
      </template>
      <div>
        <p><strong>作者：</strong>{{ selectedMessage.author }}</p>
        <p><strong>內容：</strong>{{ selectedMessage.content }}</p>
        <p><strong>時間：</strong>{{ selectedMessage.time }}</p>
      </div>
    </Modal>


    <!-- 新增留言 Modal -->
    <Modal
      :visible="showNewModal"
      :showCloseX="true"
      @close="showNewModal = false"
    >
      <template #header>
        <h3>新增留言</h3>
      </template>

      <form @submit.prevent="submitNewMessage" id="newMsgModalHeader" class="newMsgModalHeader">
        <label>
          作者：
          <input v-model.trim="newMessage.author" type="text" required />
        </label>
        <label>
          內容：
          <textarea v-model.trim="newMessage.content" required></textarea>
        </label>
      </form>

      <template #footer>
        <button type="submit" form="newMsgModalHeader">送出留言</button>
      </template>
    </Modal>

  </div>
</template>

<style scoped>
.message-list {
  list-style: none;
  padding: 0;
}

.message-item {
  margin: 1rem 0;
  padding: 1rem;
  border: 1px solid #ccc;
  border-radius: 6px;
}

.newMsgModalHeader {
  display: flex;
  flex-direction: column;
  gap: 1.5rem;
}
</style>
