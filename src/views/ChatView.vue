<template>
    <div class="main-container">
        <div v-if="isMobile && (showIntroChatBot || showUserInfo)" class="sidebar-overlay" v-on:click="closeSidebars"></div>
        <div class="mobile-header" v-if="isMobile">
            <div class="mobile-menu-icon mobile-menu-icon-left" v-on:click="toggleIntroChatBot" v-if="isMobile">☰</div>
            <div class="mobile-menu-icon mobile-menu-icon-right" v-on:click="toggleUserInfo" v-if="isMobile">💡</div>
        </div>
        <div :class="['intro-chatbot', { 'show-sidebar': showIntroChatBot }]">
            <GenAiOption />
        </div>
        <div class="chat-main-content">
            <div class="chat-container">
                <div class="chat-messages">
                    <ChatBubble 
                        v-for="(message, index) in messages"
                        :key="index"
                        :message="message.text"
                        :isUser="message.sender === 'user'"
                        :timeStamp="message.createdAt"
                    />
                    <ChatBubble
                        v-if="isGeneratingResponse"
                        message="Đang xử lý..."
                        :isUser="false"
                        isTyping="true"
                        :timeStamp="generatingTime"
                    />
                    <ChatBubble 
                        v-if="displayedBotMessage"
                        :message="displayedBotMessage"
                        :isUser="false"
                        isTyping="true"
                        :timeStamp="botMessageCreatedAt"
                    />
                </div>
                <ChatInput @send-message="handleSendMessage" />
                </div>
            </div>
        <div :class="['user-info-container', { 'show-sidebar': showUserInfo }]">
            <UserInfo />
        </div>
    </div>
    
</template>

<script>
import { onBeforeUnmount, onMounted, onUnmounted, ref } from 'vue';
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
                sender: 'bot',
                createdAt: new Date()
            },
            {
                text: 'Tôi có thể giúp gì cho bạn ?',
                sender: 'bot',
                createdAt: new Date(Date.now() - 60000)
            }
        ]);
        const sessionID = ref('12321425')
        // API url test for PROD environment
        const apiUrl = 'https://nv2muuac94.execute-api.us-east-2.amazonaws.com/dev/chat'

        // API url test for local environment
        // const apiBaseUrl = import.meta.env.VITE_API_BASE_URL || '';
        // const apiUrl = `${apiBaseUrl}/dev/chat`;
        const isMobile = ref(false);
        const showIntroChatBot = ref(false);
        const showUserInfo = ref(false); 

        const formattedApiResponse = ref('');

        const displayedBotMessage = ref('');
        const typingSpeed = ref(30);
        let typingInterval = null;
        const isGeneratingResponse = ref(false);
        const generatingTime = ref(null);

        const botMessageCreatedAt = ref(null);

        const formatResponseToVietnamese  = (response) => {
            try {
                const decodedResponse = decodeURIComponent(escape(response));
                const prefixToRemove = "Phản hồi:";
                if (decodedResponse.startsWith(prefixToRemove)) {
                    return decodedResponse.substring(prefixToRemove.length).trim();
                } else {
                    return decodedResponse
                }
            } catch (error) {
                console.error("Lỗi khi định dạng response:", error);
                return "Có lỗi xảy ra khi xử lý dữ liệu.";
            }
        }

        const startTypingEffect = () => {
            botMessageCreatedAt.value = new Date();
            let charIndex = 0;
            displayedBotMessage.value = '';
            typingInterval = setInterval(() => {
                if (charIndex < formattedApiResponse.value.length) {
                    displayedBotMessage.value += formattedApiResponse.value[charIndex];
                    charIndex++;
                } else {
                    clearInterval(typingInterval);
                    messages.value.push({ text: formattedApiResponse, sender: 'bot', createdAt: new Date() });
                    displayedBotMessage.value = '';
                    isGeneratingResponse.value = false; // Ẩn đoạn chat giả khi có response thật
                }
            }, typingSpeed.value);
        };

        const sendMessageToApi = async (inputText) => {
            isGeneratingResponse.value = true; // Hiển thị đoạn chat giả khi gửi request
            generatingTime.value = new Date(); // Lưu thời điểm bắt đầu "đợi"
            try {
                const bodyData = {
                    inputText: inputText,
                    sessionId: sessionID.value
                };
                const apiResponse = await fetch(apiUrl, {
                    method: 'POST',
                    headers: {
                        'Content-Type': 'application/json'
                    },
                    body: JSON.stringify(bodyData)
                });
                console.log('apiResponse:', apiResponse);

                if (!apiResponse.ok) {
                    const error = await apiResponse.json();
                    console.error('Lỗi gọi API:', error);
                    messages.value.push({ text: 'Đã có lỗi xảy ra khi giao tiếp với máy chủ.', sender: 'bot', createdAt: new Date() });
                    isGeneratingResponse.value = false; // Ẩn đoạn chat giả khi có lỗi
                    return;
                }

                const responseData = await apiResponse.json();
                console.log('Phản hồi từ API:', responseData);
                
                // Gọi hàm format ngay sau khi nhận được responseData
                // formattedApiResponse.value = formatResponseToVietnamese(responseData.response)
                // console.log('formattedApiResponse:', formattedApiResponse.value);

                // Xử lý dữ liệu phản hồi và thêm tin nhắn từ bot vào messages
                if (responseData && responseData.response) {
                    setTimeout(() => {
                        formattedApiResponse.value = formatResponseToVietnamese(responseData.response);
                        displayedBotMessage.value = '';
                        isGeneratingResponse.value = false; // Ẩn đoạn chat giả khi không có response hợp lệ
                        startTypingEffect();
                    }, 3000);
                    // messages.value.push({ text: formattedApiResponse, sender: 'bot' });
                } else {
                    messages.value.push({ text: 'Không nhận được phản hồi hợp lệ từ máy chủ', sender: 'bot', createdAt: new Date() })
                    isGeneratingResponse.value = false; // Ẩn đoạn chat giả khi không có response hợp lệ
                }
            } catch (error) {
                console.error('Lỗi gọi API:', error);
                messages.value.push({ text: 'Không thể kết nối đến máy chủ.', sender: 'bot', createdAt: new Date() });
                isGeneratingResponse.value = false; // Ẩn đoạn chat giả khi không có response hợp lệ
            }
        }

        const handleSendMessage = (newMessage) => {
            messages.value.push({ text: newMessage, sender: 'user', createdAt: new Date() });
            // setTimeout(() => {
            //     messages.value.push({ text: `Bạn vừa nói: "${newMessage}"` })
            // }, 1000);
            sendMessageToApi(newMessage);
        }

        const toggleIntroChatBot = () => {
            showIntroChatBot.value = !showIntroChatBot.value;
            if (showIntroChatBot.value) {
                showUserInfo.value = false;
            }
        }

        const toggleUserInfo = () => {
            showUserInfo.value = !showUserInfo.value;
            if (showUserInfo.value) {
                showIntroChatBot.value = false;
            }
        }
        
        const closeSidebars = () => {
            showUserInfo.value = false;
            showIntroChatBot.value = false;
        }

        const checkMobile = () => {
            isMobile.value = window.innerWidth <= 767;
        }

        onMounted(() => {
            checkMobile();
            window.addEventListener('resize', checkMobile);
        })

        onBeforeUnmount(() => {
            window.removeEventListener('resize', checkMobile);
        })

        onUnmounted(() => {
            clearInterval(typingInterval);
        })

        return {
            messages,
            handleSendMessage,
            sendMessageToApi,
            formatResponseToVietnamese,
            formattedApiResponse,
            displayedBotMessage,
            startTypingEffect,
            isGeneratingResponse,
            toggleIntroChatBot,
            toggleUserInfo,
            closeSidebars,
            showIntroChatBot,
            showUserInfo,
            isMobile

        }
    }
}
</script>

<style>
.main-container {
    display: flex;
    flex-direction: row;
    width: 100vw;
    height: 100vh;
    /* justify-content: space-between; */
    overflow-y: hidden;
    overflow-x: hidden;
    position: relative;
}

.chat-main-content {
    flex-grow: 1; /* Chiếm hết không gian còn lại trên desktop */
    display: flex;
    flex-direction: column;
    position: relative; /* Để chat-container có thể chiếm hết không gian */
}

.chat-container {
  display: flex;
  flex-grow: 1;
  flex-direction: column;
  /* height: 100vh; */
  /* width: 60%; */
  overflow: hidden;
  background: rgba(255, 255, 255, 0.2);
  backdrop-filter: blur(2px);
  overflow: hidden; /* Ẩn thanh cuộn mặc định */
  position: relative;
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

.intro-chatbot,
.user-info-container {
    flex: 0 0 250px;
    width: 20%;
    height: 100vh;
    overflow-y: auto;
    transition: transform 0.3s ease-in-out, visibility 0.3s ease-in-out;
}

/* Tùy chỉnh giao diện thanh cuộn (nếu muốn) */
.chat-messages::-webkit-scrollbar {
  width: 8px; /* Độ rộng thanh cuộn */
}

.chat-messages::-webkit-scrollbar-thumb {
  background-color: #ccc; /* Màu "ngón tay" thanh cuộn */
  border-radius: 4px;
}

.chat-messages::-webkit-scrollbar-track {
  background-color: #f1f1f1; /* Màu "đường ray" thanh cuộn */
}

/* Mobile Header (hidden by default, shown on mobile) */
.mobile-header {
    display: none; /* Hidden by default */
    justify-content: space-between;
    align-items: center;
    padding: 10px 20px;
    color: white;
    height: 60px; /* Chiều cao cố định cho header mobile */
    box-shadow: 0 2px 5px rgba(0,0,0,0.2);
    position: fixed; /* Giữ cố định trên cùng */
    top: 0;
    left: 0;
    width: 100%;
    z-index: 100; /* Đảm bảo nằm trên chat-container */
}

.header-title {
    font-size: 1.2em;
    font-weight: bold;
}

.mobile-menu-icon {
    font-size: 24px;
    cursor: pointer;
    line-height: 1; /* Để icon căn giữa theo chiều dọc */
}

/* Overlay khi sidebar mở trên mobile */
.sidebar-overlay {
    display: none; /* Hidden by default */
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: rgba(0, 0, 0, 0.5);
    z-index: 999; /* Below sidebars, above main content */
    transition: opacity 0.3s ease-in-out;
}

/* --- Responsive for Tablets (e.g., max-width 1024px) --- */
@media (max-width: 1024px) {
    .intro-chatbot,
    .user-info-container {
        flex: 0 0 200px; /* Slightly smaller sidebars */
        padding: 15px;
    }
}

/* --- Responsive for Mobile (e.g., max-width 767px) --- */
@media (max-width: 767px) {
    .main-container {
        flex-direction: column; /* Stack vertically on mobile */
        height: 100vh;
        width: 100vw;
    }

    /* Show mobile header */
    .mobile-header {
        display: flex; /* Display as flex on mobile */
    }

    /* Sidebar trái */
    .intro-chatbot {
        position: fixed;
        top: 0;
        left: 0;
        width: 70%; /* Chiếm 70% màn hình mobile */
        height: 100%;
        transform: translateX(-100%); /* Ẩn sidebar trái */
        z-index: 1000; /* Hiển thị trên content */
        box-shadow: 2px 0 5px rgba(0,0,0,0.2);
        padding: 20px;
        background-color: #f0f2f5;
        overflow-y: auto;
    }

    /* Sidebar phải */
    .user-info-container {
        position: fixed;
        top: 0;
        right: 0;
        width: 70%; /* Chiếm 70% màn hình mobile */
        height: 100%;
        transform: translateX(100%); /* Ẩn sidebar phải */
        z-index: 1000;
        box-shadow: -2px 0 5px rgba(0,0,0,0.2);
        padding: 20px;
        background-color: #f0f2f5;
        overflow-y: auto;
    }

    /* Show sidebar when 'show-sidebar' class is active */
    .intro-chatbot.show-sidebar {
        transform: translateX(0); /* Hiển thị sidebar trái */
    }

    .user-info-container.show-sidebar {
        transform: translateX(0); /* Hiển thị sidebar phải */
    }

    /* Show overlay when any sidebar is open */
    .sidebar-overlay {
        display: block; /* Hiển thị overlay */
    }

    /* Chat main content on mobile */
    .chat-main-content {
        flex-grow: 1; /* Chiếm hết phần còn lại của màn hình */
        width: 100%;
        height: calc(100vh - 60px); /* Lùi xuống bằng chiều cao của header */
        margin-top: 60px; /* Thụt xuống bằng chiều cao của header */
        border-left: none;
        border-right: none;
    }

    .chat-container {
        width: 100%;
        height: 100%; /* Chiếm đầy đủ chat-main-content */
    }

    .chat-messages {
        padding: 10px;
    }
}
</style>