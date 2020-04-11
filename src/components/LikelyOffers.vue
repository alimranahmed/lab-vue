<template>
  <div class="offers-container pt-2">
    <div class="px-2">
      <form method="post" @submit.prevent="fetchOffers" class="bg-white shadow-md rounded">
        <div class="grid grid-cols-8">
          <div class="col-span-2 px-3">
            <label for="url" class="label">URL</label>
            <input type="text" id="url" placeholder="URL" v-model="url" class="input-text">
          </div>
          <div class="col-span-2 px-3">
            <label for="email" class="label">Email</label>
            <input type="text" id="email" placeholder="Email" v-model="loginCredentials.email" class="input-text">
          </div>
          <div class="col px-3">
            <label for="password" class="label">Password</label>
            <input type="password" id="password" placeholder="Password" v-model="loginCredentials.password"
                   class="input-text">
          </div>
          <div class="col px-3">
            <label for="loan-amount" class="label">Loan amount</label>
            <input type="number" id="loan-amount" placeholder="Loan amount" v-model="application.loan_amount"
                   class="input-text">
          </div>
          <div class="col px-3">
            <label for="loan-term" class="label">Loan Term</label>
            <input type="number" id="loan-term" placeholder="Loan term" v-model="application.loan_term"
                   class="input-text">
          </div>
          <div class="col pt-6">
            <button type="submit" class="btn-blue">Show Quote</button>
          </div>
        </div>
      </form>
    </div>
    <div class="grid grid-cols-6">
      <div class="col-span-4 col-start-2">
        <table class="table-fixed w-full" v-if="offers.length">
          <tr>
            <th class="border px-4 py-2 w-1/2">Lender</th>
            <th class="border px-4 py-2 w-1/4">Status</th>
            <th class="border px-4 py-2 w-1/4">Total Payable</th>
          </tr>
          <tr v-for="(offer, key) in offers" :key="key">
            <td class="border px-4 py-2">{{offer.lender.name}}</td>
            <td class="border px-4 py-2">{{offer.status}}</td>
            <td class="border px-4 py-2">{{offer.total_payable}}</td>
          </tr>
        </table>

        <div class="text-center mt-10">
          <span v-if="is_requested && !offers.length">Loading...</span>
          <span v-if="!is_requested && !offers.length">No offer</span>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
  const axios = require('axios').default;
  export default {
    name: 'LikelyOffers',

    data() {
      return {
        offers: [],
        is_requested: false,
        application: {
          purpose: 'likely_offer',
          loan_amount: 5000,
          loan_term: 36,
          loan_purpose_id: 1,
        },
        loginCredentials: {
          email: 'customer@example.com',
          password: 'secret',
        },
        url: "http://ratehammer-core.test/api"
      }
    },

    mounted() {
      //this.fetchOffers();
    },

    methods: {
      fetchOffers() {
        this.is_requested = true;
        let self = this;
        this.login().then(self.submitApplication);
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

      submitApplication(token) {

        let self = this;
        let headers = {headers: {Authorization: 'Bearer ' + token}};

        axios.post(this.url + '/applications', this.application, headers)
          .then((response) => {
            self.offers = response.data.offers;
            self.is_requested = false;
            return response;
          })
          .catch((error) => {
            self.is_requested = false;
            self.offers = [];
            alert(error.response.data.message);
          });
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
