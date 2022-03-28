<template>
  <div class="d-flex flex-row justify-content-between mb-2">
    <p>{{ symbolName }}</p>
    <div class="text-end">
      <p>{{ currencySymbol }}{{ price }}</p>
      <div
        v-bind:class="[
          change < 0 ? 'change-red' : 'change-green',
          'percentage',
        ]"
      >
        <span>{{ change }}</span>
        <span class="percentage">{{ changePercent }}</span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  // eslint-disable-next-line vue/multi-word-component-names
  name: "Price",
  props: {
    symbolName: String,
    currencySymbol: String,
    price: String,
    changePercent: String,
    change: String,
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
p {
  margin-bottom: 0 !important;
}

.change-red {
  color: red;
}

.change-green {
  color: green;
}

.percentage {
  font-size: 10px;
}
</style>
