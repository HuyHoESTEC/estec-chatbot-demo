<template>
    <div class="chat-bubble" :class="{ 'user': isUser }">
        <p>{{ message }}</p>
    </div>
</template>

<script>
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
}

.chat-bubble.user {
  background-color: #2d2c72;
  color: white;
  align-self: flex-end; /* Đẩy tin nhắn người dùng sang phải */
}
</style>