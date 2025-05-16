<template>
    <div class="chat-bubble" :class="{ 'user': isUser, 'loading': isLoading }">
        <p>{{ message }}</p>
    </div>
</template>

<script>
import { onMounted, ref } from 'vue';

export default {
    name: 'ChatBubble',
    props: {
        message: {
            type: String,
            required: true
        },
        isUser: {
            type: Boolean,
            default: false
        }
    },
    setup() {
        const isLoading = ref(true);

        onMounted(() => {
            setTimeout(() => {
                isLoading.value = false;
            }, 1000)
        });

        return {
            isLoading
        }
    }
}
</script>

<style scoped>
.chat-bubble {
  background-color: #f0f0f0;
  color: #333;
  border-radius: 8px;
  padding: 10px 15px;
  margin-bottom: 8px;
  max-width: 80%;
  word-break: break-word;
  opacity: 1;
  transition: opacity 0.3s ease-in-out;
}

.chat-bubble.user {
  background-color: #2d2c72;
  color: white;
  align-self: flex-end; /* Đẩy tin nhắn người dùng sang phải */
}

.chat-bubble.loading {
  opacity: 0.6; /*Làm mờ đi một chút */
  animation: pulse 1.5s infinite alternate; /* Áp dụng animation pulse */
}

@keyframes pulse {
  0% {
    opacity: 0.6;
    transform: scale(1);
  }
  100% {
    opacity: 0.8;
    transform: scale(1.05);
  }
}
</style>