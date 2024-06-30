<script setup lang="ts">
const channels = {
  "Saalssa Gamer_wqwer1": {
    channel: "Saalssa Gamer",
    description: "Banished video 2",
    url: "https://www.youtube.com/watch?v=-tQyf4c6Te0",
    icon: "https://yt3.googleusercontent.com/TQWxtkdXy8bt4STJvcNCAGZf5trHeUwlZ6rTGFqaTZ8oxqvalbrIlmPzSy1Tm2gD0faJUJJh=s160-c-k-c0x00ffffff-no-rj",
  },
};

// In prod: check if secure, then use wss://
const { status, data, send, open, close } = useWebSocket(
  `ws://${location.host}/api/websocket`
);

const history = ref<string[]>([]);
watch(data, (newValue) => {
  history.value.push(`server: ${newValue}`);
  const channel = channels["Saalssa Gamer_wqwer1"];
  if (channel) {
    sendNotification(channel);
  }
});

const message = ref("");
function sendData() {
  history.value.push(`client: ${message.value}`);
  send(message.value);
  message.value = "";
}

async function sendNotification(channel: any) {
  try {
    // await Notification.requestPermission();
    const userHasPermission = Notification.permission === "granted";

    if (!userHasPermission) {
      await Notification.requestPermission();
    }

    setTimeout(() => {
      //   const beep = new Audio(audio_file);
      //   beep.volume = 0.2;
      //   beep.play();
      new Notification(channel.channel, {
        body: channel.description,
        icon: channel.icon,
        silent: true,
      });
    }, 5000);
  } catch (error) {
    console.error(error);
  }
}
</script>

<template>
  <div>
    <h1>WebSocket - let's go!</h1>
    <form @submit.prevent="sendData">
      <input v-model="message" />
      <button type="submit">Send</button>
    </form>
    <div>
      <p v-for="entry in history">{{ entry }}</p>
    </div>
  </div>
</template>
