<template>
  <div class="offers-container">

    <div class="login-form">
      <form method="post" @submit.prevent="">
        <label for="url">URL</label>
        <input type="text" id="url" placeholder="URL" v-model="url">
        <label for="email">Email</label>
        <input type="text" id="email" placeholder="Email" v-model="loginCredentials.email">
        <label for="password">Password</label>
        <input type="password" id="password" placeholder="Password" v-model="loginCredentials.password">
      </form>
    </div>

    <div class="application-form">
      <form method="post" @submit.prevent="fetchOffers">
        <label for="loan-amount">Loan amount</label>
        <input type="number" id="loan-amount" placeholder="Loan amount" v-model="application.loan_amount">
        <label for="loan-term">Loan Term</label>
        <input type="number" id="loan-term" placeholder="Loan term" v-model="application.loan_term">
        <button type="submit">Show Lender Quote</button>
      </form>
    </div>

    <table class="offers-table" v-if="offers.length">
      <tr>
        <th>Lender</th>
        <th>Status</th>
        <th>Total Payable</th>
      </tr>
      <tr v-for="(offer, key) in offers" :key="key">
        <td>{{offer.lender.name}}</td>
        <td>{{offer.lender.name}}</td>
        <td>{{offer.status}}</td>
        <td>{{offer.total_payable}}</td>
      </tr>
    </table>

    <div v-else>Loading...</div>

  </div>
</template>

<script>
  const axios = require('axios').default;
  export default {
    name: 'LikelyOffers',
    props: {
      msg: String,
      url: {default: "http://ratehammer-core.test/api"},
    },

    data() {
      return {
        offers: [],
        application: {
          purpose: 'likely_offer',
          loan_amount: 5000,
          loan_term: 36,
          loan_purpose_id: 1,
        },
        loginCredentials: {
          email: 'customer@example.com',
          password: 'secret',
        }
      }
    },

    mounted() {
      this.fetchOffers();
    },

    methods: {
      fetchOffers() {
        let self = this;
        this.login().then(function (token) {
          self.submitApplication(token);
        });
      },

      login() {
        if (this.isAlreadyLoggedIn()) {
          return this.tokenPromise();
        }

        let self = this;

        return axios.post(this.url + '/login', this.loginCredentials)
          .then((response) => {
            return self.storeLoginResponse(response);
          })
          .catch((error) => {
            alert(error.response.data.message)
          });
      },

      isAlreadyLoggedIn() {
        return sessionStorage.token && sessionStorage.url === this.url;
      },

      tokenPromise() {
        return new Promise(function (resolve, reject) {
          if (sessionStorage.token) {
            resolve(sessionStorage.token);
          } else {
            reject('Url not found');
          }
        });
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
            return response;
          })
          .catch((error) => {
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
