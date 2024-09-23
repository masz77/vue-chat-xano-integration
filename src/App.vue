<template>
  <div class="container">
    <div class="d-flex flex-column align-items-stretch flex-shrink-0 bg-white">
      <div class="d-flex align-items-center flex-shrink-0 p-3 link-dark text-decoration-none border-bottom">
            <input class="fs-5 fw-semibold" v-model="username" />
          </div>
          <div class="list-group list-group-flush border-bottom scrollarea">
            <div class="list-group-item list-group-item-action py-3 lh-tight" v-for="message in messages" :key="message">
            <div class="d-flex w-100 align-items-center justify-content-between">
              <strong class="mb-1">{{ message.username }}</strong>
            </div>
            <div class="col-10 mb-1 small">{{ message.message }}</div>
          </div>
        </div>
      </div>
      <form @submit.prevent="submit">
        <input class="form-control" p laceholder="Write a messag
e" v-model="message" />
    </form>
  </div>
</template>

<script>
import { ref } from 'vue'; //onMounted
import { XanoClient } from '@xano/js-sdk';

export default {
  name: 'App',
  setup() {
    const username = ref('username');
    const messages = ref([]);
    const message = ref('');

    // onMounted(() => {
      
    // })

    const xanoClient = new XanoClient({
      instanceBaseUrl: "https://api.sndq.io/",
      realtimeConnectionHash: "Sntqvf19YV2Ipx6jetwHc3hFa9c",
    });

    console.log(xanoClient);

    var channel = xanoClient.channel("activity_chat/not_eb9d5a5281f14d2bb1857da28e70d844").on((action) => {
      console.log('Receive action', action);
      if (action?.action == "message") {
        messages.value.push({
          message: action.payload.message,
          username: username
        });
      }
    });

    channel.message({ message: "message on mount" });
    const submit = async () => {
      channel.message({ message: message.value });
      message.value = '';
    }

    return {
      username,
      messages,
      message,
      submit
      
    }
  }
}
</script>

<style>
.scrollarea {
  min-height: 500px;
}
</style>
