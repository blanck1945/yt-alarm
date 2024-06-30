<script setup lang="ts">
interface Channels {
  [key: string]: {
    channel: string;
    description: string;
    url: string;
    icon: string;
  };
}

const channels: Channels = {
  Saalssa_Gamer_wqwer1: {
    channel: "Saalssa Gamer",
    description: "Banished video 2",
    url: "https://www.youtube.com/watch?v=-tQyf4c6Te0",
    icon: "https://yt3.googleusercontent.com/TQWxtkdXy8bt4STJvcNCAGZf5trHeUwlZ6rTGFqaTZ8oxqvalbrIlmPzSy1Tm2gD0faJUJJh=s160-c-k-c0x00ffffff-no-rj",
  },
};

const creators = [
  {
    id: 1,
    name: "Saalssa Gamer",
    image:
      "https://yt3.googleusercontent.com/TQWxtkdXy8bt4STJvcNCAGZf5trHeUwlZ6rTGFqaTZ8oxqvalbrIlmPzSy1Tm2gD0faJUJJh=s160-c-k-c0x00ffffff-no-rj",
    socials: [
      {
        id: 1,
        name: "youtube",
        url: "https://www.youtube.com/@Saalssagamer/featured",
      },
    ],
  },
];

// In prod: check if secure, then use wss://
const protocol = process.env.NODE_ENV === "production" ? "wss" : "ws";
const { data, status, ws, send, close, open } = useWebSocket(
  `${protocol}://${location?.host}/api/websocket`
);
console.log(data);
const history = ref<string[]>([]);
watch(data, (newValue) => {
  console.warn("Lo que digas", data.value);
  console.warn("Lo que digas", ws.value);
  history.value.push(`server: ${newValue}`);
  const channel = channels[newValue];
  if (channel) {
    sendNotification(channel);
  }
});

// const message = ref("");
// function sendData() {
//   if (status === "CLOSED") open();
//   //history.value.push(`client: ${message.value}`);
//   send(message.value);
//   message.value = "";

//   if (status === "OPEN") close();
// }

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
    <p v-for="entry in history">{{ entry }}</p>
  </div>
  <div class="grid grid-cols-4 m-4">
    <div
      class="border rounded-md p-4 flex flex-col items-center"
      v-for="creator of creators"
      :key="creator.id"
    >
      <img :src="creator.image" class="rounded-full" />
      <h2>{{ creator.name }}</h2>
      <div class="flex">
        <a
          v-for="social of creator.socials"
          :key="social.id"
          :href="social.url"
          class="mr-2"
        >
          {{ social.name }}
        </a>
      </div>
    </div>
  </div>
</template>
