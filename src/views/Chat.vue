<template>
  <b-container class="bv-example-row">
    <b-form-group>
      <b-form-select class="mb-3" v-model="room" @change="selectRoom">
        <b-form-select-option :value="null"
          >Please select Room</b-form-select-option
        >
        <b-form-select-option value="html">HTML</b-form-select-option>
        <b-form-select-option value="css">CSS</b-form-select-option>
        <b-form-select-option value="js">JS</b-form-select-option>
      </b-form-select>
    </b-form-group>
    <b-row>
      <b-col cols="2">
        <div class="chat">
          <div class="chat-window">
            <p class="room">User 1</p>
            <hr />
            <p class="room">User 2</p>
            <hr />
          </div>
        </div>
      </b-col>
      <b-col cols="10">
        <div class="chat">
          <div class="chat-window">
            <div class="output">
              <p v-if="typing.isTyping">
                <em>{{ typing.username }} is typing a message...</em>
              </p>
              <p v-for="(value, index) in messages" :key="index">
                <strong>{{ value.username }} :</strong>
                {{ value.message }}
              </p>
            </div>
          </div>
          <input
            class="message"
            v-model="message"
            type="text"
            placeholder="Message"
          />
          <button class="send" @click="sendMessage">Send</button>
        </div>
      </b-col>
    </b-row>
  </b-container>
</template>

<script>
import io from "socket.io-client";

export default {
  name: "Chat",
  data() {
    return {
      // TAMBAHKAN SYNTAX YANG ADA DI DALAM INI
      // =========================================
      socket: io.connect("https://backend-web6.fwebdev.online", {
        path: "/api2/socket.io"
      }),
      // socket: io.connect("http://localhost:3001", {
      //   path: "/api2/socket.io"
      // }),
      // =========================================
      username: "",
      message: "",
      messages: [],
      room: "",
      oldRoom: "",
      typing: {
        isTyping: false
      }
    };
  },
  watch: {
    message(value) {
      value
        ? this.socket.emit("typing", {
            username: this.username,
            room: this.room,
            isTyping: true
          })
        : this.socket.emit("typing", {
            room: this.room,
            isTyping: false
          });
    }
  },
  created() {
    if (!this.$route.params.username) {
      this.$router.push("/");
    }
    this.username = this.$route.params.username;
    // console.log(this.$route.params);
    this.socket.on("chatMessage", data => {
      console.log(data);
      this.messages.push(data);
    });
    this.socket.on("typingMessage", data => {
      this.typing = data;
    });
  },
  methods: {
    sendMessage() {
      // const setData = {
      //   username: this.username,
      //   message: this.message
      // };
      // console.log(setData);
      // this.socket.emit("globalMessage", setData);
      // this.socket.emit("privateMessage", setData);
      // this.socket.emit("broadcastMessage", setData);
      // ===================
      const setData = {
        username: this.username,
        message: this.message,
        room: this.room
      };
      // [1] menjalankan socket io untuk mendapatkan realtimenya
      this.socket.emit("roomMessage", setData);
      // [2] menjalankan proses axios post data ke table chat
      // this.postMessage(setData)
      this.message = "";
    },
    selectRoom(data) {
      console.log(data);
      // [1] join room socket io
      if (this.oldRoom) {
        console.log("sudah pernah masuk ke room " + this.oldRoom);
        console.log("dan akan masuk ke room " + data);
        this.socket.emit("changeRoom", {
          username: this.username,
          room: data,
          oldRoom: this.oldRoom
        });
        this.oldRoom = data;
      } else {
        console.log("belum pernah masuk ke ruang manapun");
        console.log("dan akan masuk ke room " + data);
        this.socket.emit("joinRoom", {
          username: this.username,
          room: data
        });
        this.oldRoom = data;
      }
      // [2] proses get data dari database dan akan menyimpan ke dalam variabel messages proses axios
    }
  }
};
</script>

<style scoped>
.chat {
  max-width: 600px;
  border: 1px solid #ddd;
  box-shadow: 1px 3px 5px rgba(0, 0, 0, 0.05);
  border-radius: 2px;
}

.room {
  margin: 10px 0;
}

h2 {
  font-size: 18px;
  padding: 10px 20px;
  color: #575ed8;
}

.mario-chat {
  max-width: 600px;
  margin: 30px auto;
  border: 1px solid #ddd;
  box-shadow: 1px 3px 5px rgba(0, 0, 0, 0.05);
  border-radius: 2px;
}

.chat-window {
  height: 400px;
  overflow: auto;
  background: #f9f9f9;
}

.output p {
  text-align: left;
  padding: 14px 0px;
  margin: 0 20px;
  border-bottom: 1px solid #e9e9e9;
  color: #555;
}

.feedback p {
  color: #aaa;
  padding: 14px 0px;
  margin: 0 20px;
}

.output strong {
  color: #575ed8;
}

label {
  box-sizing: border-box;
  display: block;
  padding: 10px 20px;
}

input {
  padding: 10px 20px;
  box-sizing: border-box;
  background: #eee;
  border: 0;
  display: block;
  width: 100%;
  background: #fff;
  border-bottom: 1px solid #eee;
  font-family: Nunito;
  font-size: 16px;
}

button {
  background: #575ed8;
  color: #fff;
  font-size: 18px;
  border: 0;
  padding: 12px 0;
  width: 100%;
  border-radius: 0 0 2px 2px;
}
</style>
