<template>
  <div>
    <div class="jp-card-container">
      <div class="jp-card" :class="classCard">
        <div class="jp-card-front">
          <div class="jp-card-logo jp-card-elo">
            <div class="e">e</div>
            <div class="l">l</div>
            <div class="o">o</div>
          </div>
          <div class="jp-card-logo jp-card-visa">visa</div>
          <div class="jp-card-logo jp-card-mastercard">MasterCard</div>
          <div class="jp-card-logo jp-card-maestro">Maestro</div>
          <div class="jp-card-logo jp-card-amex"></div>
          <div class="jp-card-logo jp-card-discover">discover</div>
          <div class="jp-card-logo jp-card-dankort">
            <div class="dk">
              <div class="d"></div>
              <div class="k"></div>
            </div>
          </div>
          <div class="jp-card-lower">
            <div class="jp-card-shiny"></div>
            <div class="jp-card-cvc jp-card-display" :class="classDisplay['cvc']">{{ display.cvc }}</div>
            <div
              class="jp-card-number jp-card-display"
              :class="classDisplay['number']"
            >{{ display.number }}</div>
            <div
              class="jp-card-name jp-card-display"
              :class="classDisplay['name']"
            >{{ display.name }}</div>
            <div
              class="jp-card-expiry jp-card-display"
              :class="classDisplay['expiry']"
              :data-before="options.monthYear"
              :data-after="options.validDate"
            >{{ display.expiry }}</div>
          </div>
        </div>
        <div class="jp-card-back">
          <div class="jp-card-bar"></div>
          <div class="jp-card-cvc jp-card-display" :class="classDisplay['cvc']">{{ display.cvc }}</div>
          <div class="jp-card-shiny"></div>
        </div>
      </div>
    </div>
    <slot></slot>
  </div>
</template>

<script>
import Vue from "vue";
import Payment from "payment/lib";

let options = {
  formatting: false,
  monthYear: "month/year",
  validDate: "valid\nthru",
  cardTypes: [
    "amex",
    "dankort",
    "dinersclub",
    "discover",
    "jcb",
    "laser",
    "maestro",
    "mastercard",
    "unionpay",
    "visa",
    "visaelectron",
    "elo"
  ],
  inputTypes: ["number", "name", "expiry", "cvc"],
  placeholders: {
    number: "•••• •••• •••• ••••",
    cvc: "•••",
    expiry: "••/••",
    name: "Full Name"
  }
};

let classDisplay = {};
options.inputTypes.forEach(type => {
  classDisplay[type] = {
    "jp-card-focused": false,
    "jp-card-valid": false,
    "jp-card-invalid": false
  };
});

let stateCard = {
  toInvert: false
};

Vue.directive("card-focus", {
  inserted: function(el) {
    const toggleFocusState = type => () => {
      classDisplay[el.name]["jp-card-focused"] = type === "focus";

      stateCard.toInvert = type === "focus" && el.name === "cvc";
    };

    el.onfocus = toggleFocusState("focus");
    el.onblur = toggleFocusState("blur");

    if (el.name === "cvc") Payment.formatCardCVC(el);
  }
});

const isValid = {
  number: val => Payment.fns.validateCardNumber(val),
  name: val => val !== "",
  expiry: val => {
    let objVal = Payment.fns.cardExpiryVal(val);
    return Payment.fns.validateCardExpiry(objVal.month, objVal.year);
  },
  cvc: (val, cardType) => Payment.fns.validateCardCVC(val, cardType)
};

const fns = {
  formatCardExpiry: val =>
    val.replace(/^([0-9]{2})\/?([0-9]{2,4})$/gm, "$1 / $2")
};

export default {
  name: "Card",
  props: ["value", "validate"],
  data() {
    return {
      cardType: null,
      options,
      stateCard,
      classDisplay
    };
  },
  computed: {
    classCard: function() {
      let obj = {
        "jp-card-flipped": this.stateCard.toInvert
      };

      this.cardType = Payment.fns.cardType(this.value.number);

      obj["jp-card-identified"] = !!this.cardType;

      let knownFlag = false;
      options.cardTypes.forEach(type => {
        if (this.cardType === type) obj["jp-card-" + type] = knownFlag = true;
      });

      if (!knownFlag) obj["jp-card-unknown"] = true;

      return obj;
    },
    display: function() {
      this.value.number = Payment.fns.formatCardNumber(
        this.value.number.replace(/[^0-9]/gim, "")
      );

      this.value.name = this.value.name
        .toUpperCase()
        .replace(/[^A-Z\s]/gim, "");

      this.value.expiry = fns.formatCardExpiry(
        this.value.expiry.replace(/[^0-9]/gim, "")
      );

      options.inputTypes.forEach(type => {
        let valided = isValid[type](this.value[type], this.cardType);
        classDisplay[type]["jp-card-valid"] = valided;
        classDisplay[type]["jp-card-invalid"] = !valided;

        this.validate[type]["valid"] = valided;
        if (!valided) {
          this.validate[type]["message"] =
            type + " is invalid. please enter valid value!";
        } else {
          this.validate[type]["message"] = "";
        }
      });

      let value = Object.assign({}, this.value);

      Object.keys(value).forEach(key => !value[key] && delete value[key]);

      value = Object.assign({}, options.placeholders, value);

      return {
        number: value.number,
        name: value.name,
        expiry: value.expiry.replace(/(\s+)/g, ""),
        cvc: value.cvc
      };
    }
  },
  created() {
    if (options.formatting) {
      if (!Payment.fns.validateCardCVC(this.value.cvc))
        console.error("CVC number isn't valid:", this.value.cvc);
      if (!Payment.fns.validateCardExpiry(this.value.expiry))
        console.error("Expiration date isn't valid:", this.value.expiry);
      if (!Payment.fns.validateCardNumber(this.value.number))
        console.error("Card number isn't valid:", this.value.number);
    }
  },
  mounted() {}
};
</script>

<style lang="sass">
  @import "../scss/card";

</style>