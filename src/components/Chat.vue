<template>
  <div class="message">
    <div v-if="!selectChat" class="nothing">
      <h1>Click in a chat for see more information</h1>
    </div>
    <div v-if="selectChat" class="chat">
      <div class="chatInfoContact">
        <div><img :src="contactImage" /></div>
        <div>
          <div>{{ contactName }}</div>
          <div>{{ lastSeen }}</div>
        </div>
      </div>
      <div class="textMessages">
        <div v-for="(text, index) in textMessages" v-bind:key="index">
          <div
            class="text"
            v-bind:class="text.sender !== 1 ? 'recieveMessage' : 'ownMessages'"
          >
            <div class="time">
              <p>{{ text.time }}</p>
            </div>
            <div class="infoMessage">
              <div><img :src="contactImage" /></div>
              <div class="nameAndText">
                <p>{{ text.sender !== 1 ? contactName : profileName }}</p>
                <p>{{ text.text }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="spaceWritting">
        <div class="space">
          <input
            id="message"
            type="text"
            placeholder="Say something..."
            class="spaceToWrite"
            v-on:keyup.enter="sendMessage()"
          />
        </div>
        <div class="buttons">
          <button
            class="send"
            v-on:click="
              sendMessage(
                contacts.filter((contact) => contact.title === contactName).id
              )
            "
          >
            <img src="../assets/send.png" class="imageSend" /></button
          ><button class="adjunto">
            <img src="../assets/clip.png" class="imageSend" />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>
<script>
export default {
  name: "Chat",
  data: function () {},
  props: {
    selectChat: Boolean,
    contactName: String,
    lastSeen: String,
    contactImage: String,
    textMessages: Array,
    contacts: Array,
    sendMessage: Function,
    profileName: String,
  },
};
</script>
<style scoped>
.message {
  width: 85%;
  height: 100vh;
  background-color: rgb(248, 248, 248);
}
.chat {
  overflow: hidden;
}
.chatInfoContact {
  display: flex;
  align-items: center;

  border-bottom: solid 0.5px black;
  height: 10vh;
  margin-left: 20px;
  margin-right: 20px;
}
.textMessages {
  display: flex;
  flex-direction: column;
  margin-left: 20px;
  margin-right: 20px;
  overflow-y: scroll;
  height: 80vh;
}
.text {
  margin: 10px;
  background-color: white;
  padding: 20px;
  border-radius: 0.5rem;
}
.infoMessage {
  display: flex;
  align-items: center;
}
.nameAndText > p {
  margin-top: 10px;
}
.buttons {
  width: 10%;
  display: flex;
  align-items: center;
  justify-content: center;
}
.spaceWritting {
  height: 10vh;
  display: flex;
  background-color: white;
}
.spaceToWrite {
  width: 100%;
  height: 100%;
  padding-left: 10px;
  border: none;
  display: flex;
}
.space {
  width: 90%;
}
.time {
  text-align: right;
  font-size: 10px;
}
.send {
  padding: 5px;
  background-color: rgb(0, 187, 45);
  border: none;
  border-radius: 1em;
  cursor: pointer;
}
.adjunto {
  padding: 5px;
  background-color: rgb(204, 102, 102);
  border: none;
  border-radius: 1em;
}
.imageSend {
  width: 30px;
}
.ownMessages {
  float: right;
}
.recieveMessage {
  float: left;
}
.nothing {
  width: 100%;

  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
}
</style>
