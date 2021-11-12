<template>
  <div class="form">
    <label for="notifykey">Notify Key</label>
    <input name="notifykey" type="text" v-model="notifyKey">
    <label for="title">Notification Title</label>
    <input name="title" type="text" v-model="title">
    <label for="body">Notification Body</label>
    <input name="body" type="text" v-model="body">
    <label for="color">Color (hex code)</label>
    <input name="color" type="text" v-model="color">
    <button @click="send()">Send Message</button>
  </div>
</template>

<script lang="ts">
import {defineComponent, ref, watch} from 'vue';

export default defineComponent({
  name: 'HelloWorld',
  setup() {
    const notifyKey = ref(localStorage.getItem("pufferyNotifyKey") || "");
    const title = ref('');
    const body = ref('');
    const color = ref(localStorage.getItem("pufferyNotifyColor") || "");

    (window as any).ipcRenderer.on("messageSend", (event: any, data: any) => {
      
      if(data.id) {
        alert("Notification successfully received!");
        title.value = "";
        body.value = "";
      } else {
        alert("Something went wrong" );
      }
    });

    watch(notifyKey, (key) => {
      localStorage.setItem("pufferyNotifyKey", key);
    })
    watch(color, (c) => {
      localStorage.setItem("pufferyNotifyColor", c);
    })

    const send = async () => {
      await (window as any).ipcRenderer.send("sendMessageToPufferyChannel", {notifyKey: notifyKey.value, title: title.value,body:body.value,color: color.value});
    };

    return {title, body, color, notifyKey, send};
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.form {
  display: flex;
  flex-direction: column;
  text-align: left;
}
.form > input + * {
  margin-top: 2rem;
}
a {
  color: #42b983;
}
</style>
