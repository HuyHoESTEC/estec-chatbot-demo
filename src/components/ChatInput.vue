<template>
    <div class="chat-input">
        <textarea class="input-message" v-on:keyup.enter="handleEnter" v-model="newMessage" placeholder="Hỏi bất cứ điều gì..." />
        <button v-on:click="sendMessage" :disabled="!newMessage.trim()">Gửi</button>
    </div>
</template>

<script>
import { ref } from 'vue';

export default {
    name: 'ChatInput',
    emits: ['send-message'],
    setup(_, { emit }) {
        const newMessage = ref('');

        const sendMessage = () => {
            if (newMessage.value.trim()) {
                emit('send-message', newMessage.value);
                newMessage.value = '';
            }
        }

        const handleEnter = (event) => {
          if (event.shiftKey) {
            // Nếu Shift + Enter được nhấn, thêm một dòng mới vào textarea
            newMessage.value += '\n';
          } else {
            sendMessage();
          }
        }

        return {
            newMessage,
            sendMessage,
            handleEnter
        }
    }
}
</script>

<style scoped>
.chat-input {
  display: flex;
  align-items: center;
  padding: 10px;
  border-top: 1px solid #ccc;
  border-radius: 25px;
}

.chat-input textarea {
  flex-grow: 1;
  padding: 8px;
  border: 1px solid #ccc;
  border-radius: 5px;
  margin-right: 10px;
}

.chat-input button {
  padding: 8px 15px;
  background-color: #2d2c72;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

.chat-input button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.input-message {
  background-color: white;
  color: black;
  border: none;
  outline: none;
  padding: 10px 14px;
  font-size: 16px;
  border-radius: 8px;
  width: 100%;
  box-sizing: border-box;
  height: 70px;
  resize: none; /* Cho phép người dùng kéo để thay đổi chiều cao */
  min-height: 40px; /* Chiều cao tối thiểu */
  line-height: 1.5; /* Khoảng cách dòng */
  overflow-y: auto; /* Hiển thị scrollbar khi nội dung vượt quá chiều cao */
  white-space: pre-wrap; /* Giữ nguyên khoảng trắng và xuống dòng */
}

.input-message:hover,
.input-message:focus {
  border: none;
  outline: none;
}
</style>