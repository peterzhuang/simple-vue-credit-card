<template>
  <div id="app " class="creditCardWrapper">
    <v-container>
      <v-layout text-xs-center wrap>
        <v-flex xs12>
          <h1>vue-credit-card</h1>
        </v-flex>

        <v-flex xs12>
          <card v-model="cardDetail" :validate="validation"></card>
        </v-flex>

        <v-flex xs12 md6>
          <label for="number" class="card-form-field__label">Card Number</label>
          <input
            name="number"
            placeholder="Card number"
            type="tel"
            v-model="cardDetail.number"
            :class="{'isError' : !validation['number'].valid && validation['number'].touched}"
            @change="validation['number'].touched = true"
            v-card-focus
          >
          <div
            class="message"
            v-show="!validation['number'].valid && validation['number'].touched"
          >{{validation['number'].message}}</div>
        </v-flex>
        <v-flex xs12 md6>
          <label for="name" class="card-form-field__label">Full Name</label>
          <input
            name="name"
            placeholder="Full name"
            type="text"
            v-model="cardDetail.name"
            :class="{'isError' : !validation['name'].valid && validation['name'].touched}"
            @change="validation['name'].touched = true"
            v-card-focus
          >
          <div
            class="message"
            v-show="!validation['name'].valid && validation['name'].touched"
          >{{validation['name'].message}}</div>
        </v-flex>
        <v-flex xs12 md6>
          <label for="expiry" class="card-form-field__label">Expiry: MM/YY</label>
          <input
            name="expiry"
            placeholder="MM/YY"
            type="tel"
            :class="{'isError' : !validation['expiry'].valid && validation['expiry'].touched}"
            @change="validation['expiry'].touched = true"
            v-model="cardDetail.expiry"
            v-card-focus
          >
          <div
            class="message"
            v-show="!validation['expiry'].valid && validation['expiry'].touched"
          >{{validation['expiry'].message}}</div>
        </v-flex>
        <v-flex xs12 md6>
          <label for="cvc" class="card-form-field__label">CVC</label>
          <input
            name="cvc"
            placeholder="CVC"
            type="number"
            v-model="cardDetail.cvc"
            :class="{'isError' : !validation['cvc'].valid && validation['cvc'].touched}"
            @change="validation['cvc'].touched = true"
            v-card-focus
          >
          <div
            class="message"
            v-show="!validation['cvc'].valid && validation['cvc'].touched"
          >{{validation['cvc'].message}}</div>
        </v-flex>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
import card from "./CreditCard.vue";

let defaultProps = {
  number: "",
  name: "Yuchuan Zhuang",
  expiry: "12/2018",
  cvc: "12"
};

export default {
  name: "CreditCardUserInput",
  components: {
    card
  },
  data() {
    return {
      cardDetail: defaultProps,
      validation: {
        number: {
          touched: false,
          valid: true,
          message: ""
        },
        name: {
          touched: false,
          valid: true,
          message: ""
        },
        expiry: {
          touched: false,
          valid: true,
          message: ""
        },
        cvc: {
          touched: false,
          valid: true,
          message: ""
        }
      }
    };
  }
};
</script>

<style scoped>
.creditCardWrapper {
  width: 450px;
  margin: 5px auto;
}
input {
  display: block;
  padding-left: 0;
  font-size: 18px;
  border: 1px solid #999;
}
input.isError {
  border: 1px solid #ff3860;
  border-radius: 3px;
}
.message {
  color: #ff3860;
}
.card-form-field__label {
  display: block;
  margin-bottom: 4px;
  text-align: left;
}
</style>