<script setup>
import { defineProps, defineEmits } from 'vue'

/*
  ＃詳解標記區（知識點統整）
  ● 組件封裝觀念：Modal 可直接寫在父層，但封裝為獨立元件可提高重用性與邏輯集中，是良好架構設計的實踐。
  ● props + emit 搭配邏輯：父層以 visible 控制是否顯示，Modal 發出 close 通知事件，維持單向資料流。
  ● v-if 控制 vs 組件生命週期：使用 v-if 確保 Modal 完全卸載，有助於重設內部狀態（如表單清空）。
  ● @click.self 的使用：只有點擊 backdrop(div.modal-backdrop) 本體才會觸發關閉，避免誤觸 Modal 內容。
  ● 可預設又可覆蓋的 slot 設計：footer slot 提供預設按鈕，也允許父層覆蓋，保持彈性與 UX 控制權。
*/

const props = defineProps({
  visible: Boolean,
  showCloseX: {
    type: Boolean,
    default: true
  },
  closeOnBackdropClick: {
    type: Boolean,
    default: true
  }
  // 請問這個closeOnBackdropClick, 是點開新增留言等視窗的時候會出現的深色背景對嗎？
  // 沒有看到它在哪裡被使用, 請問它是怎麼跟著按鈕顯示的?
  // 還是說我按下按鈕的那一刻, 就觸發prop內容的這三塊變為true或false?
})

const emit = defineEmits(['close'])

const close = () => {
  emit('close')
}

const handleBackdropClick = () => {
  if (props.closeOnBackdropClick) {
    close()
  }
}
// 這邊我理解是關閉視窗時, 可以透過點擊深色背景操控的內容
</script>

<template>
  <div v-if="visible" class="modal-backdrop" @click.self="handleBackdropClick">
    <div class="modal">
      <!-- Header slot -->
      <div class="modal-header">
        <div class="modal-header-content">
          <slot name="header"></slot>
        </div>
        <button v-if="showCloseX" class="modal-close" @click="close">&times;</button>
      </div>

      <!-- default slot -->
      <div class="modal-body">
        <slot></slot>
      </div>

      <!-- Footer slot -->
      <div class="modal-footer">
        <slot name="footer">
          <button @click="close">關閉</button>
        </slot>
      </div>
    </div>
  </div>
</template>

<style scoped>
.modal-backdrop {
  position: fixed;
  top: 0; left: 0; right: 0; bottom: 0;
  background: rgba(0, 0, 0, 0.5);
  display: flex;
  align-items: center;
  justify-content: center;
  z-index: 999;
}
.modal {
  background: white;
  width: 400px;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 4px 10px rgba(0,0,0,0.2);
}
.modal-header, .modal-footer {
  padding: 1rem;
  background: #f2f2f2;
  display: flex;
}
.modal-header-content {
  flex-grow: 1;
}
.modal-body {
  padding: 1rem;
}
.modal-close {
  border: none;
  background: none;
  font-size: 1.2rem;
  cursor: pointer;

}
</style>
