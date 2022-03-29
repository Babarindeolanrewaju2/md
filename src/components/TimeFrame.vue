<template>
  <div
    class="d-flex flex-row justify-content-between mx-auto"
    style="width: 70%"
  >
    <div
      :class="['click-time', timeSelection === '15' ? 'button-theme' : '']"
      @click="setTimeFrame('15')"
    >
      15m
    </div>
    <div
      :class="['click-time', timeSelection === '60' ? 'button-theme' : '']"
      @click="setTimeFrame('60')"
    >
      1H
    </div>
    <div
      :class="['click-time', timeSelection === 'D' ? 'button-theme' : '']"
      @click="setTimeFrame('D')"
    >
      1D
    </div>
    <div
      :class="['click-time', timeSelection == 'W' ? 'button-theme' : '']"
      @click="setTimeFrame('W')"
    >
      1W
    </div>
    <div
      :class="['click-time', timeSelection == 'M' ? 'button-theme' : '']"
      @click="setTimeFrame('M')"
    >
      1M
    </div>
  </div>
</template>

<script>
export default {
  name: "TimeFrame",
  props: {
    timeSelection: String,
  },
  methods: {
    setTimeFrame(event) {
      this.$emit("setTimeFrame", event);
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.click-time,
.button-theme {
  cursor: pointer;
  padding: 2px;
  padding-left: 10px;
  padding-right: 10px;
  color: gray;
  font-size: 12px;
}

.button-theme {
  background-color: lightblue;
  border-radius: 5px;
  color: black;
}
</style>
