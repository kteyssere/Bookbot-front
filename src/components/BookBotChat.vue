<template>
  <div class="chat-container">
    <h1 class="title">📚 BookBot</h1>
    <div class="chat-window">
      <div
        v-for="(msg, index) in messages"
        :key="index"
        :class="['message', msg.sender]"
      >
        <strong>{{ msg.sender === 'user' ? 'Vous' : 'BookBot' }} :</strong>
        <span>{{ msg.text }}</span>
      </div>
    </div>

    <form @submit.prevent="sendMessage" class="input-form">
    <input
        v-model="userInput"
        type="text"
        placeholder="Posez une question sur un livre..."
        class="input"
    />
    <button class="send-btn">Envoyer</button>
    </form>

  </div>
</template>

<script>
export default {
  name: "BookBotChat",
  data() {
    return {
      userInput: "",
      messages: []
    };
  },
  methods: {
    async sendMessage() {
      const text = this.userInput.trim();
      if (!text) return;
      this.messages.push({ sender: "user", text });
      this.userInput = "";

      try {
        const response = await fetch("http://localhost:5000/chat", {
          method: "POST",
          headers: { "Content-Type": "application/json" },
          body: JSON.stringify({ message: text })
        });
        const data = await response.json();
        this.messages.push({ sender: "bot", text: data.reply });
      } catch (err) {
        this.messages.push({ sender: "bot", text: "Erreur : impossible de contacter BookBot." });
      }
    }
  }
};
</script>

<style scoped>
.chat-container {
  max-width: 600px;
  margin: auto;
  padding: 2rem;
  background-color: #ffffff;
  color: #222222;
  border-radius: 12px;
  box-shadow: 0 0 12px rgba(0, 0, 0, 0.1);
  font-family: Arial, sans-serif;
}

.title {
  text-align: center;
  margin-bottom: 1rem;
  font-weight: bold;
  font-size: 1.8rem;
  color: #1a1a1a;
}

.chat-window {
  height: 400px;
  overflow-y: auto;
  background: #f9f9f9;
  padding: 1rem;
  margin-bottom: 1rem;
  border-radius: 8px;
  border: 1px solid #ddd;
  box-sizing: border-box;
}

.message {
  margin: 0.5rem 0;
  line-height: 1.4;
}

.message.user {
  text-align: right;
  color: #333333;
}

.message.bot {
  text-align: left;
  color: #0066cc;
  font-weight: 600;
}

form {
  display: flex;
  gap: 0.5rem;
}

.input {
  flex: 1;
  padding: 0.6rem 0.8rem;
  border-radius: 6px;
  border: 1px solid #ccc;
  font-size: 1rem;
  outline: none;
  box-sizing: border-box;
}

.input-form {
  display: flex;
  gap: 0.5rem;
}

.input:focus {
  border-color: #0066cc;
  box-shadow: 0 0 4px rgba(0, 102, 204, 0.4);
}

.send-btn {
  padding: 0.6rem 1.2rem;
  background-color: #0066cc;
  color: white;
  border: none;
  border-radius: 6px;
  font-weight: 600;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.send-btn:hover {
  background-color: #004999;
}

</style>
