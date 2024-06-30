<script>
export default {
  data() {
    return {
      message: "",
    };
  },
  methods: {
    async sendData() {
      const protocol = process.env.NODE_ENV === "production" ? "wss" : "ws";
      const { send } = useWebSocket(
        `${protocol}://${location?.host}/api/websocket`
      );
      send(this.message);
      this.message = "";
    },
  },
};
</script>

<template>
  <div
    class="flex flex-col items-center gap-4 border rounded-md w-1/2 m-auto mt-4 py-2"
  >
    <h1>Notifica a tus seguidores tus nuevos videos</h1>
    <form class="flex flex-col items-center gap-2" @submit.prevent="sendData">
      <div class="flex flex-col">
        <label class="text-gray-600 text-sm" for=""
          >Envia la URL del nuevo video</label
        >
        <input class="border rounded-md" v-model="message" />
      </div>
      <button
        class="bg-violet-600 text-white rounded-md transition-all px-5 py-1 hover:bg-violet-500 disabled:bg-violet-200"
        type="submit"
      >
        Enviar
      </button>
    </form>
  </div>
</template>
