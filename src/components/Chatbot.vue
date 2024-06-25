<template>
    <div class="chat-container">
      <div class="messages">
        <div v-for="(message, index) in messages" :key="index" class="message" :class="message.type">
          {{ message.text }}
        </div>
        <div v-if="isTyping" class="message bot typing-indicator">
          <span></span><span></span><span></span>
        </div>
      </div>
      <div class="input-area">
        <input type="text" v-model="userInput" @keydown.enter="sendMessage" @input="typing = true" placeholder="Type a message...">
        <button @click="sendMessage">Send</button>
      </div>
      <Notification v-if="error" :message="errorMessage" />
    </div>
  </template>
  
  <script>
  import { ref, onMounted, nextTick, watch } from 'vue';
  import Notification from './Notification.vue';
  
  export default {
    name: 'Chatbot',
    components: {
      Notification
    },
    setup() {
      const messages = ref([]);
      const userInput = ref('');
      const error = ref(false);
      const errorMessage = ref('');
      const isTyping = ref(false);
      const typing = ref(false);
  
      watch(userInput, (newVal, oldVal) => {
        if (newVal === '') {
          typing.value = false;
        }
      });
  
      const sendMessage = () => {
        if (userInput.value.trim() === '') {
          showError('Message cannot be empty');
          return;
        }
  
        messages.value.push({ text: userInput.value, type: 'user' });
        userInput.value = '';
        typing.value = false;
  
        isTyping.value = true;
        setTimeout(() => {
          messages.value.push({ text: 'Bot response', type: 'bot' });
          isTyping.value = false;
          scrollToBottom();
        }, 1000);
      };
  
      const showError = (message) => {
        errorMessage.value = message;
        error.value = true;
        setTimeout(() => {
          error.value = false;
        }, 3000);
      };
  
      const scrollToBottom = () => {
        nextTick(() => {
          const chatContainer = document.querySelector('.messages');
          chatContainer.scrollTop = chatContainer.scrollHeight;
        });
      };
  
      onMounted(scrollToBottom);
  
      return {
        messages,
        userInput,
        error,
        errorMessage,
        isTyping,
        typing,
        sendMessage
      };
    }
  };
  </script>
  