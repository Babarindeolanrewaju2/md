<template>
  <div class="d-flex">
    <div class="currency-item ma-ri">
      <div :class="firstFlag" class="circle"></div>
    </div>

    <div class="currency-item ma-ri">
      <div :class="secondFlag" class="circle"></div>
    </div>
    <p v-if="exchangeName" class="exchange-name-theme">{{ exchangeName }}</p>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Flags",
  props: {
    firstFlag: String,
    secondFlag: String,
    exchangeName: String,
  },
  data() {
    return {
      symbolSelected: "",
    };
  },
  methods: {
    setSymbol() {
      this.$emit("onChangeSymbol", this.symbolSelected.displaySymbol);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.ma-ri {
  margin-right: 10px;
}
.exchange-name-theme {
  background-color: gainsboro;
  padding: 3px;
  border-radius: 5px;
  font-size: 12px;
  height: 24px;
}

.circle {
  background-position: 50% 50%;
  width: 25px;
  height: 25px;
  border-radius: 50%;
}
</style>
