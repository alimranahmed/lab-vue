<template>
  <div>
    <h2 class="align-center" v-html="message"></h2>
  </div>
</template>

<script>
  const axios = require('axios').default;
  import Pusher from 'pusher-js';

  export default {
    name: "PusherClient",
    data() {
      return {
        message: "Ready!",
        pusher: null,
        loginCredentials: {
          contact: 'doctor@example.com',
          password: 'secret',
        },
        url: "https://admindev.doctime.com.bd/api"
        // url: "http://doctime.test/api"
      }
    },
    mounted() {
      let self = this;
      this.login().then(self.tryPusher);
    },
    methods: {
      tryPusher(token) {
        this.message = "Connecting to pusher...";
        let self = this;
        this.pusher = new Pusher('62a2fe5af494d2f46a1e', {
          cluster: 'ap2',
          authEndpoint: self.url + '/broadcasting/auth',
          encrypted: true,
          auth: {
            headers: {
              Accept: 'application/json',
              Authorization: 'Bearer ' + token
            },
          }
        });
        this.message += "<br>Connected to pusher!";

        let presenceChannel = this.pusher.subscribe('presence-doctor-queue-1');
        console.log(presenceChannel);

        presenceChannel.bind('pusher:subscription_error', function (status) {
          this.message += "<br>Error subscription to channel: presence-test-1; status: " + status;
        });

        presenceChannel.bind('pusher:subscription_succeeded', function (members) {
          this.message += "<br>Subscribed successfully; member: " + members;
        });

        presenceChannel.bind('pusher:member_added', function (member) {
          this.message += "<br> New member added: " + member;
        });

        presenceChannel.bind('pusher:member_removed', function (member) {
          this.message += "<br>Member removed: " + member;
        });

        presenceChannel.bind('App\\Events\\PresenceEvent', function (data) {
          console.log(presenceChannel, data);
          self.message += "<br>Event triggered: App\\Events\\PresenceEvent; Data: " + JSON.stringify(data);
        });
      },


      login() {
        if (this.isAlreadyLoggedIn()) {
          return Promise.resolve(sessionStorage.token);
        }

        let self = this;

        return axios.post(this.url + '/login', this.loginCredentials)
          .then(self.storeLoginResponse)
          .catch((error) => {
            alert(error.response.data.message)
          });
      },

      isAlreadyLoggedIn() {
        return sessionStorage.token && sessionStorage.url === this.url;
      },

      storeLoginResponse(response) {
        sessionStorage.url = this.url;
        sessionStorage.token = response.data.data.access.token;
        return sessionStorage.token;
      },
    }
  }
</script>

<style scoped>

</style>
