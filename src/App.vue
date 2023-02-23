<template>
  <div class="chat-app">
    <div class="chat-window">
      <div class="chat-messages">
        <div v-for="message in messages" :key="message.id" class="chat-message">
          <p v-if="message.isBot" class="bot-message">{{ message.text }}</p>
          <p v-else class="user-message">{{ message.text }}</p>
        </div>
      </div>
      <form class="chat-form" @submit.prevent="sendMessage">
        <input type="text" v-model="inputText" placeholder="Type your message..." />
        <button type="submit">Send</button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'ChatApp',
  data() {
    return {
      inputText: '',
      messages: [],
      sessionId: null,
      apiEndpoint: 'https://api.openai.com/v1',
      apiKey: 'YOUR_API_KEY_HERE',
    };
  },
  methods: {
    async sendMessage() {
      const message = {
        id: Date.now(),
        text: this.inputText,
        isBot: false,
      };
      this.messages.push(message);
      this.inputText = '';

      const payload = {
        prompt: message.text,
        max_tokens: 50,
        n: 1,
        stop: ['\n'],
        session_id: this.sessionId,
      };
      const headers = { Authorization: `Bearer ${this.apiKey}` };
      const { data } = await axios.post(`${this.apiEndpoint}/completions`, payload, { headers });

      const botMessage = {
        id: Date.now(),
        text: data.choices[0].text.trim(),
        isBot: true,
      };
      this.messages.push(botMessage);
      this.sessionId = data.id;
    },
  },
};
</script>

<style>
.chat-app {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.chat-window {
  width: 400px;
  height: 600px;
  background-color: #fff;
  box-shadow: 0px 0px 20px 0px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
}

.chat-messages {
  flex-grow: 1;
  padding: 20px;
  overflow-y: scroll;
}

.chat-message {
  margin: 10px 0;
}

.bot-message {
  background-color: #eee;
  padding: 10px;
  border-radius: 10px;
  display: inline-block;
}

.user-message {
  background-color: #007bff;
  color: #fff;
  padding: 10px;
  border-radius: 10px;
  display: inline-block;
}

.chat-form {
  display: flex;
  padding: 10px;
}

.chat-form input[type='text'] {
  flex-grow: 1;
  border: none;
  outline: none;
  padding:10px;
border-radius: 5px;
}

.chat-form button {
background-color: #007bff;
color: #fff;
border: none;
outline: none;
padding: 5px 10px;
border-radius: 5px;
margin-left: 10px;
cursor: pointer;
}
</style>
