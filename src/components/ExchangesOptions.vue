<template>
  <div>
    <select @change="setExchange($event)" v-model="exchangeOption">
      <option value="" disabled>Select exchange</option>
      <option
        v-for="(exchange, index) in exchanges"
        :key="index"
        :value="exchange"
      >
        {{ exchange }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "ExchangesOptions",
  props: {
    exchanges: Array,
  },
  data() {
    return {
      exchangeOption: "",
    };
  },
  methods: {
    setExchange(event) {
      this.$emit("onChangeExchange", event.target.value);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
select {
  width: 200px;
  padding: 5px;
  box-sizing: border-box;
}
</style>
