<template>
  <div class="d-flex flex-row justify-content-between">
    <div @click="setTimeFrame('15')">15m</div>
    <div @click="setTimeFrame('60')">1H</div>
    <div @click="setTimeFrame('D')">1D</div>
    <div @click="setTimeFrame('W')">1W</div>
    <div @click="setTimeFrame('M')">1M</div>
  </div>
</template>

<script>
export default {
  name: "TimeFrame",
  methods: {
    setTimeFrame(event) {
      this.$emit("setTimeFrame", event);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
