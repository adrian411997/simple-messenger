<template>
  <div class="main">
    <Chat
      :select-chat="selectChat"
      :contact-name="contactName"
      :last-seen="lastSeen"
      :contact-image="contactImage"
      :text-messages="textMessages"
      :contacts="contacts"
      :send-message="sendMessage"
      :profile-name="profileName"
    />
    <div class="infoPestañas">
      <div class="contacts">
        <div
          class="pestañaMessage elegido"
          id="sms"
          v-on:click="changeShow($event)"
        >
          Message
        </div>
        <div class="pestañaContacts" id="cont" v-on:click="changeShow2($event)">
          Contacts
        </div>
      </div>
      <div v-if="showMessages" class="infoMessages">
        <div
          class="contactInfo"
          v-for="(message, index) in messages"
          v-bind:key="index"
          v-on:click="
            changeSelectChat(
              contacts.filter((contact) => contact.id === message.users[1])[0]
                .title,
              contacts.filter((contact) => contact.id === message.users[1])[0]
                .img,
              contacts.filter((contact) => contact.id === message.users[1])[0]
                .date,
              message
            )
          "
        >
          <div>
            <img
              :src="
                contacts.filter((contact) => contact.id === message.users[1])[0]
                  .img
              "
            />
          </div>
          <div>
            <p>
              {{
                contacts.filter((contact) => contact.id === message.users[1])[0]
                  .title
              }}
            </p>
            <p>{{ message.lastMessageTime }}</p>
          </div>
        </div>
      </div>
      <div v-if="!showMessages" class="infoContacts">
        <div>
          <div class="searchContact">
            <input placeholder="Search..." class="searchInput" />
          </div>
          <div
            class="contactInfo"
            v-for="(contact, index) in contacts"
            v-bind:key="index"
            v-on:click="
              changeSelectChat(
                contact.title,
                contact.img,
                contact.date,
                messages.filter((message) => message.users[1] === contact.id)[0]
              )
            "
          >
            <div><img :src="contact.img" /></div>
            <div>{{ contact.title }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
import Chat from "./Chat.vue";
export default {
  name: "Template",
  components: {
    Chat,
  },
  data: function () {
    return {
      senderMyMessages: 1,
      showMessages: true,
      showContacts: false,
      contacts: null,
      messages: [],
      selectChat: false,
      contactName: null,
      contactImage: null,
      lastSeen: null,
      textMessages: [],
      profileName: "Sarah Kortney",
    };
  },
  beforeCreate() {
    fetch("https://api.coloredstrategies.com/contacts")
      .then((response) => response.json())
      .then((response) => (this.contacts = response.data));
    fetch("https://api.coloredstrategies.com/conversations")
      .then((response) => response.json())
      .then((response) => (this.messages = response.data));
  },
  methods: {
    changeShow: function (event) {
      this.showMessages = true;
      this.showContacts = false;
      let pestaña = document.getElementById("cont");
      if (pestaña.classList.contains("elegido")) {
        pestaña.classList.remove("elegido");
        event.target.classList.add("elegido");
      }
    },
    changeShow2: function (event) {
      this.showMessages = false;
      this.showContacts = true;
      let pestaña = document.getElementById("sms");
      if (pestaña.classList.contains("elegido")) {
        pestaña.classList.remove("elegido");
        event.target.classList.add("elegido");
      }
    },
    changeSelectChat: function (name, image, lastSeen, messages) {
      this.selectChat = true;
      this.contactName = name;
      this.contactImage = image;
      this.lastSeen = lastSeen;
      this.textMessages = messages === undefined ? "" : messages.messages;
    },
    sendMessage: function () {
      let idEncontrado = this.contacts.filter(
        (contact) => contact.title === this.contactName
      );

      let date = new Date();
      let hour = date.getHours() < 10 ? "0" + date.getHours() : date.getHours();
      let minutes =
        date.getMinutes() < 10 ? "0" + date.getMinutes() : date.getMinutes();
      let time = hour + ":" + minutes;
      let newText = {
        sender: this.senderMyMessages,
        time: time,
        text: document.getElementById("message").value,
      };
      let newConversation = {
        users: [1, idEncontrado[0].id],
        lastMessageTime: time,
        messages: [newText],
      };
      let pastConversation = this.messages.filter(
        (message) =>
          JSON.stringify(message.users) ===
          JSON.stringify(newConversation.users)
      );
      if (pastConversation.length > 0) {
        this.messages
          .filter(
            (message) =>
              JSON.stringify(message.users) ===
              JSON.stringify(newConversation.users)
          )[0]
          .messages.push(newText);
        this.messages.filter(
          (message) =>
            JSON.stringify(message.users) ===
            JSON.stringify(newConversation.users)
        )[0].lastMessageTime = time;
      } else {
        this.messages.unshift(newConversation);
        this.textMessages = this.messages.filter(
          (message) => message.users[1] === idEncontrado[0].id
        )[0].messages;
      }
      document.getElementById("message").value = "";
    },
  },
};
</script>

<style scoped>
.main {
  display: flex;
}
.pestañaMessage {
  cursor: pointer;
  padding-top: 10px;
}
.pestañaContacts {
  cursor: pointer;
  padding-top: 10px;
}
.infoPestañas {
  width: 15%;
  margin-left: 20px;
  margin-right: 20px;
  overflow: hidden;
}
.contacts {
  display: flex;
  text-align: center;
  padding-top: 20px;
}
.contacts > div {
  width: 50%;
}
.contactInfo {
  display: flex;
  margin-top: 10px;
  padding: 10px 0px;
  border-bottom: solid 1px black;
  cursor: pointer;
}
img {
  border-radius: 50%;
  width: 30px;
}

.searchContact {
  margin-top: 10px;
}
.searchInput {
  width: 90%;
  padding: 10px;
  border-radius: 2em;
  border: solid;
  border-width: 0.5px;
}
.elegido {
  border-top: 1px solid blue;
}
</style>
