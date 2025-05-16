<template>
    <div class="main-container">
        <div class="intro-chatbot">
            <GenAiOption />
        </div>
        <div class="chat-container">
            <div class="chat-messages">
                <ChatBubble 
                    v-for="(message, index) in messages"
                    :key="index"
                    :message="message.text"
                    :isUser="message.sender === 'user'"
                />
            </div>
            <ChatInput @send-message="handleSendMessage" />
        </div>
        <div class="user-info-container">
            <UserInfo />
        </div>
    </div>
    
</template>

<script>
import { ref } from 'vue';
import ChatBubble from '../components/ChatBubble.vue';
import ChatInput from '../components/ChatInput.vue';
import GenAiOption from './GenAiOption.vue';
import UserInfo from './UserInfo.vue';

export default {
    name: 'ChatView',
    components: {
        ChatBubble,
        ChatInput,
        GenAiOption,
        UserInfo
    },
    setup() {
        const messages = ref([
            {
                text: 'Xin chào, tôi là trợ lý ESTEC chatbot ',
                sender: 'bot'
            },
            {
                text: 'Tôi có thể giúp gì cho bạn ?',
                sender: 'bot'
            }
        ]);

        const handleSendMessage = (newMessage) => {
            messages.value.push({ text: newMessage, sender: 'user' });
            setTimeout(() => {
                messages.value.push({ text: `Bạn vừa nói: "${newMessage}"` })
            }, 1000);
        }

        return {
            messages,
            handleSendMessage
        }
    }
}
</script>

<style>
.main-container {
    display: flex;
    flex-direction: row;
    width: 100%;
    height: auto;
    /* justify-content: space-between; */
}

.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 60%;
  overflow: hidden;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(2px);
}

.chat-messages {
  flex-grow: 1;
  padding: 10px;
  overflow-y: auto;
  display: flex;
  flex-direction: column; /* Sắp xếp tin nhắn theo chiều dọc */
  align-items: flex-start; /* Căn chỉnh các tin nhắn từ bên trái */
}

.chat-messages > .chat-bubble.user {
  align-self: flex-end;
}

.intro-chatbot {
    width: 20%;
    height: 100vh;
}

.user-info-container {
    width: 20%;
    height: 100vh;
}
</style>