<template>
    <div class="container">
      <!-- msg-header section starts -->
      <div class="msg-header">
        <div class="container1">
          <img :src="user2Img" class="msgimg" />
          <div class="active">
            <p>Dialog BOT</p>
          </div>
        </div>
      </div>
      <!-- msg-header section ends -->
  
      <!-- Chat inbox  -->
      <div class="chat-page">
        <div class="msg-inbox">
          <div class="chats">
            <!-- Message container -->
            <div class="msg-page">
              <div v-for="(message, index) in messages" :key="index" :class="[message.type === 'outgoing' ? 'outgoing-chats' : 'received-chats']">
                <div :class="[message.type === 'outgoing' ? 'outgoing-chats-img' : 'received-chats-img']">
                  <img :src="message.type === 'outgoing' ? user1Img : user2Img" />
                </div>
                <div :class="[message.type === 'outgoing' ? 'outgoing-msg' : 'received-msg']">
                  <div :class="[message.type === 'outgoing' ? 'outgoing-chats-msg' : 'received-msg-inbox']">
                    <p>{{ message.text }}</p>
                    <span class="time">{{ message.time }}</span>
                  </div>
                </div>
              </div>
              <div v-if="isBotTyping" class="received-chats">
                <div class="received-chats-img">
                  <img :src="user2Img" />
                </div>
                <div class="received-msg">
                  <div class="received-msg-inbox typing-indicator">
                    <span></span><span></span><span></span>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <!-- msg-bottom section -->
          <div class="msg-bottom">
            <div class="input-group">
              <input
                id="input-text"
                type="text"
                class="form-control input-msg"
                placeholder="Write message..."
                v-model="userInput"
                @keydown.enter="sendMessage"
              />
              <span class="input-group-text send-icon" @click="sendMessage">
                <!-- <button>send</button> -->
                <img :src="sendIcon" class="send-icon" />
                <i class="bi bi-send"></i>
              </span>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  
  <script>
  import axios from 'axios';
  import user1Img from '@/assets/user1.png';
  import user2Img from '@/assets/user2.ico';
  import sendIcon from '@/assets/icons8-send-48.png'
  export default {
    data() {
      return {
        user1Img,
        user2Img,
        sendIcon,
        userInput: '',
        isBotTyping: false,
        messages: []
      };
    },
    created() {
      // Initialize axios-mock-adapter
      this.axios = axios;
    },
    methods: {
      async sendMessage() {

        
        if (this.userInput.trim() === '') return;
  
        // Add user message to the messages array
        this.addMessage(this.userInput, 'outgoing');
        const userMessage = this.userInput;
        console.log(user1Img);
        this.userInput = '';
  
        // Show bot typing indicator
        this.isBotTyping = true;
      //   try {
      //   // Make POST request using Axios
      //   const response = await axios.post('http://192.168.1.15:5000/your-endpoint', { message: userMessage });

      //   // Handle response data
      //   this.isBotTyping = false;
      //   console.log(response.data); // Log the data received from the server

      //   // Assuming you have a method or logic to add the received message to your chat interface
      //   this.addMessage(response.data.message, 'received');
      // } catch (error) {
      //   console.error('Error posting data:', error);
      //   this.isBotTyping = false;
      //   // Handle any errors that occur during the Axios request
      // }
      try {
        const response = await fetch('http://127.0.0.1:5000/your-endpoint', {
          method: 'POST',
          headers: {
            'Content-Type': 'application/json',
          },
          body: JSON.stringify({ message: userMessage }),
        });

        if (!response.ok) {
          throw new Error(`HTTP error! Status: ${response.status}`);
        }

        const data = await response.json(); // Parse JSON response

        // Handle response data
        this.isBotTyping = false;
        console.log('Response from server:', data);

        // Assuming you have a method or logic to add the received message to your chat interface
        this.addMessage(data.message, 'received');
      } catch (error) {
        console.error('Error posting data:', error);
        this.isBotTyping = false;
        // Handle any errors that occur during the fetch request
      }
        },
      addMessage(text, type) {
        this.messages.push({
          text,
          type,
          time: `${new Date().toLocaleTimeString()} | ${new Date().toLocaleDateString()}`
        });
  
        this.$nextTick(() => {
          const msgPage = this.$el.querySelector('.msg-page');
          msgPage.scrollTop = msgPage.scrollHeight;
        });
      }
    }
  };
  </script>
  
  
  <style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Roboto&display=swap');
* {
    box-sizing: border-box;
    font-family: 'Roboto', sans-serif;
}

/* Styling the main container */
.container {
    width: 100%;
    margin: auto;
    margin-top: 2rem;
    letter-spacing: 0.5px;
    min-width: 80%;
    min-height: 50%;
    height: 70%;
}

img {
    width: 50px;
    vertical-align: middle;
    border-style: none;
    border-radius: 100%;
}
/* Styling the msg-header container */
.msg-header {
    border: 1px solid #ccc;
    width: 100%;
    min-height: 15%;
    height: 10%;
    border-bottom: none;
    display: inline-block;
    background-color: #efefef;
    margin: 0;
    border-top-left-radius: 20px;
    border-top-right-radius: 20px;
}
/* Styling the profile picture */
.msgimg {
    margin-left: 2%;
    float: left;
}

.container1 {
    width: 270px;
    height: auto; 
    float: left;
    margin: 10px 0px;
}

/* styling user-name */
.active {
    width: 150px;
    float: left;
    color: black;
    font-weight: bold;
    margin: 15px 0 0 10px;
    height: 10%;
 
}
/* Styling the inbox */
.chat-page {
    margin-top:-7px;
    padding: 0 0 50px 0;
}

.msg-inbox {
    border: 1px solid #ccc;
    width:100%;
    overflow: hidden;
    border-bottom-left-radius: 20px;
    border-bottom-right-radius: 20px;
}


.chats {
    padding: 30px 15px 0 25px;
}

.msg-page {
    max-height: 500px;
    min-height: 300px;
    overflow-y: auto;
}

/* Styling the msg-bottom container */
 .msg-bottom {
        border-top: 1px solid #ccc;
        position: relative;
        height: 11%;
        background-color: rgb(239 239 239);
    }
/* Styling the input field */
    .input-group {
        /* float: right; */
        margin-top: 3px;
        margin-left: 147px;
        outline: none !important;
        border-radius: 20px;
        width: 61% !important;
        background-color: #fff;
        position: relative;
        display: flex;
        flex-wrap: wrap;
        align-items: stretch;
        box-shadow: rgba(0, 0, 0, 0.35) 0px 5px 15px;
    }

    #input-text {
      margin: 6px 0 0 10px;
      padding: 10px 0 0 10px ;
    }
    
    
    .input-group>.form-control {
        position: relative;
        flex: 1 1 auto;
        width: 1%;
        margin-bottom: 0;
    }
    
    .form-control {
        border: none !important;
        border-radius: 20px !important;
        display: block;
        height: calc(2.25rem + 2px);
        padding: 0.375rem 0.75rem;
        font-size: 1rem;
        line-height: 1.5;
        color: #495057;
        background-color: #fff;
        background-clip: padding-box;
        transition: border-color .15s ease-in-out, box-shadow .15s ease-in-out;
    }
    
    .input-group-text {
        background: transparent !important;
        border: none !important;
        display: flex;
        align-items: center;
        padding: 0.375rem 0.75rem;
        margin-bottom: 0;
        font-size: 1.5rem;
        font-weight: b;
        line-height: 1.5;
        color: #495057;
        text-align: center;
        white-space: nowrap;
        background-color: #e9ecef;
        border: 1px solid #ced4da;
        border-radius: 0.25rem;
        font-weight: bold !important;
        cursor: pointer;
    }
    
    input:focus {
        outline: none;
        border: none !important;
        box-shadow: none !important;
    }
    
    .send-icon {
        font-weight: bold !important;
    }

/* Styling the avatar  */
received-chats-img {
    display: inline-block;
    width: 50px;
    float: left;
}

.received-msg {
    display: inline-block;
    padding: 0 0 0 10px;
    vertical-align: top;
    width: 92%;
}
.received-msg-inbox {
    width: 57%;
}
.input-msg {
  margin-top: 2opx;
}

.received-msg-inbox p {
    background: #efefef none repeat scroll 0 0;
    border-radius: 10px;
    color: #646464;
    font-size: 14px;
    margin-left: 1rem;
    padding: 1rem;
    width: 100%;
    box-shadow: rgb(0 0 0 / 25%) 0px 5px 5px 2px;
}
    p {
    overflow-wrap: break-word;
}

/* Styling the msg-sent time  */
.time {
    color: #777;
    display: block;
    font-size: 12px;
    margin: 8px 0 0;
}
.outgoing-chats {
    overflow: hidden;
    margin: 26px 20px;
}

.outgoing-chats-msg p {
    background-color: #3a12ff;
    background-image: linear-gradient(45deg, #ee087f 0%, #DD2A7B 25%, #9858ac 50%, #8134AF 75%, #515BD4 100%);
    color: #fff;
    border-radius: 10px;
    font-size: 14px;
    color: #fff;
    padding: 5px 10px 5px 12px;
    width: 100%;
    padding: 1rem;
    box-shadow: rgb(0 0 0 / 25%) 0px 2px 5px 2px;
}
.outgoing-chats-msg {
        float: right;
        width: 46%;
    }

/* Styling the avatar */
.outgoing-chats-img {
    display: inline-block;
    width: 50px;
    float: right;
    margin-right: 1rem;
}

.typing-indicator span {
  background-color: #ccc;
  border-radius: 50%;
  display: inline-block;
  height: 10px;
  margin: 0 1px;
  width: 10px;
}

.typing-indicator {
  display: flex;
  justify-content: center;
  align-items: center;
}

.typing-indicator span:nth-child(1) {
  animation: typing 1s infinite;
}

.typing-indicator span:nth-child(2) {
  animation: typing 1s infinite 0.2s;
}

.typing-indicator span:nth-child(3) {
  animation: typing 1s infinite 0.4s;
}

@keyframes typing {
  0%, 80%, 100% {
    transform: translateY(0);
  }
  40% {
    transform: translateY(-10px);
  }
}

@media only screen and (max-device-width: 850px) {
    *,
    .time {
        font-size: 28px;
    }
    img {
        width: 65px;
    }
    .msg-header {
        height: 5%;
    }
    .msg-page {
        max-height: none;
    }
    .received-msg-inbox p {
        font-size: 28px;
    }
    .outgoing-chats-msg p {
        font-size: 28px;
    }
}

@media only screen and (max-device-width: 450px) {
    *,
    .time {
        font-size: 28px;
    }
    img {
        width: 65px;
    }
    .msg-header {
        height: 5%;
    }
    .msg-page {
        max-height: none;
    }
    .received-msg-inbox p {
        font-size: 28px;
    }
    .outgoing-chats-msg p {
        font-size: 28px;
    }
}


  </style>
  