<template>
  <div>
    <select @change="setSymbol()" v-model="symbolSelected">
      <option value="" disabled>Select a symbol</option>
      <option
        v-for="(symbol, index) in symbols"
        v-bind:key="index"
        :value="symbol"
      >
        {{ symbol.displaySymbol }}
      </option>
    </select>
  </div>
</template>

<script>
export default {
  name: "SymbolsOptions",
  props: {
    symbols: Array,
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
select {
  width: 170px;
  padding: 5px;
  box-sizing: border-box;
}
</style>
