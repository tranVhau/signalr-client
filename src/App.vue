<template>
  <div>
    <div class="custom-container vh-100 p-2">
      <div class="message-box-wrapper h-100">
        <div class="list-user-field">
          <div class="p-2 border-end border-dark h-100">
            <h5
              class="p-2 border-bottom border-dark text-center font-weight- text-uppercase"
            >
              user list
            </h5>
            <div class="p-2">
              <UserCard v-for="(item, index) in userList" :key="index">
                {{ item }}</UserCard
              >
            </div>
          </div>
        </div>
        <div class="chat-box-field">
          <MessageCard
            v-for="(item, index) in messageList"
            :isSelfMessage="userName === item.user"
            :user="item.user"
            :time="item.time"
            :key="index"
          >
            {{ item.message }}</MessageCard
          >
          <div ref="chat-bottom"></div>
        </div>
        <div class="input-field">
          <b-input-group :prepend="this.userName" class="p-2">
            <b-form-input v-model="message"> </b-form-input>
            <b-input-group-append>
              <b-button variant="outline-dark" @click="sendMessageHandler"
                >Send</b-button
              >
            </b-input-group-append>
          </b-input-group>
        </div>
      </div>
    </div>
    <SetNameModalVue v-if="isSetName" @setUsernameHandler="setUserName" />
  </div>
</template>

<script>
import SetNameModalVue from "./components/SetNameModal.vue";
import MessageCard from "./components/MessageCard.vue";
import UserCard from "./components/UserCard.vue";
import * as signalR from "@microsoft/signalr";

export default {
  name: "App",
  components: { UserCard, MessageCard, SetNameModalVue },

  data() {
    return {
      isSetName: true,
      connection: null,
      message: "",
      userName: "",
      messageList: [],
      userList: [],
    };
  },
  created() {
    const newConn = new signalR.HubConnectionBuilder()
      .withUrl("https://localhost:7013/signalr-server")
      .configureLogging(signalR.LogLevel.Information)
      .build();

    async function start() {
      try {
        await newConn.start();
        console.log("Client Connected.");
      } catch (err) {
        console.log(err);
      }
    }
    window.addEventListener("beforeunload", this.leaveRoomHandler);
    newConn.onclose(async () => {
      // await start();
      await newConn.stop();
    });
    this.connection = newConn;
    start();
  },

  mounted() {
    this.connection.on("ReceiveMessage", (message, user, sentTime) => {
      this.messageList.push({ message: message, user: user, time: sentTime });
      this.backToChatFooter();
    });

    this.connection.on("UserJoin", (user, groupUsers) => {
      //handle join room notify ...
      console.log("joined", groupUsers);
      this.userList = groupUsers;
      console.log(user + " just joined room chat");
    });

    this.connection.on("UserLeave", function (user, groupUsers) {
      //handle leave room notify
      console.log("left", groupUsers);
      this.userList = groupUsers;
      console.log(user + " just left room chat");
    });
  },

  methods: {
    joinRoomHandler() {
      try {
        this.connection.invoke("JoinChat", this.userName);
      } catch (error) {
        console.log("on joinRoomHandler", error);
      }
    },

    leaveRoomHandler() {
      try {
        this.connection.invoke("LeaveChat", this.userName);
      } catch (error) {
        console.log("on leaveRoomHandler", error);
      }
    },

    setUserName(username) {
      this.userName = username;
      this.connection.invoke;
      this.isSetName = false;
      this.joinRoomHandler();
    },
    sendMessageHandler() {
      if (!this.message.trim() || !this.userName) return;
      this.connection
        .send("SendMessage", this.message, this.userName)
        .catch(function (err) {
          return console.error(err.toString());
        });

      this.message = "";
    },

    backToChatFooter() {
      this.$refs["chat-bottom"].scrollIntoView({ behavior: "smooth" });
    },
  },
};
</script>

<style lang="scss" scoped>
.message-box-wrapper {
  display: grid;
  grid-template-columns: 1fr 4fr;
  border: 1px solid rgb(50, 50, 50);
  border-radius: 6px;
  gap: 4px;
  .list-user-field {
    grid-row: 1 / 12;
    overflow: auto;
  }

  .chat-box-field {
    overflow-y: scroll;
    grid-row: 1/11;
  }
  .input-field {
    grid-row: 11 / 12;
    align-self: end;
  }
}
</style>
