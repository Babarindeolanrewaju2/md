/* eslint-disable vue/require-v-for-key */
<template>
  <div id="app">
    <div class="container">
      <div class="row px-2 py-5 align-items-center">
        <div class="col-lg-3 col-md-3 col-12">
          <!-- Exchange select -->
          <div class="d-block py-2">
            <Exchanges
              v-bind:exchanges="exchanges"
              @onChangeExchange="onChangeExchange"
            />
          </div>

          <!-- Symbol select -->
          <div class="d-block py-2">
            <Symbols
              v-bind:symbols="symbols"
              @onChangeSymbol="onChangeSymbol"
            />
          </div>
        </div>

        <div class="col-lg-8 col-md-8 col-12">
          <!-- Currency flags -->
          <Flags
            :firstFlag="firstFlag"
            :secondFlag="secondFlag"
            :exchangeName="exchangeName"
          />

          <!-- price -->
          <Price
            :symbolName="symbolName"
            :currencySymbol="currencySymbol"
            :price="price"
            :changePercent="changePercent"
            :change="change"
            :caret="caret"
          />

          <!-- chart -->
          <apexchart
            height="350"
            :options="chartOptions"
            :series="series"
          ></apexchart>

          <!-- time selection -->
          <TimeFrame
            @setTimeFrame="setTimeFrame"
            :timeSelection="timeSelection"
          />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";
import axios from "axios";
import getSymbolFromCurrency from "currency-symbol-map";
import "currency-flags/dist/currency-flags.css";
import Exchanges from "./components/ExchangesOptions.vue";
import Symbols from "./components/SymbolsOptions.vue";
import TimeFrame from "./components/TimeFrame.vue";
import Flags from "./components/Flags.vue";
import Price from "./components/Price.vue";

export default {
  name: "App",
  data() {
    return {
      exchanges: [],
      exchangeName: "",
      symbolName: "",
      symbols: [],
      chart: [],
      series: [],
      firstFlag: "",
      secondFlag: "",
      currencySymbol: "",
      price: "",
      timeSelection: "D",
      change: null,
      caret: null,
      changePercent: "",
      chartOptions: {
        chart: {
          type: "candlestick",
        },
        title: {
          align: "left",
        },
        xaxis: {
          type: this.timeSelection,
        },
        yaxis: {
          tooltip: {
            enabled: true,
          },
        },
      },
    };
  },
  components: {
    Exchanges,
    Symbols,
    TimeFrame,
    Flags,
    Price,
    apexchart: VueApexCharts,
  },
  methods: {
    onChangeExchange(event) {
      this.exchangeName = event;
    },
    onChangeSymbol(event) {
      this.symbolName = event;
      let flagsArray = this.symbolName.split("/");
      let symbol = getSymbolFromCurrency(flagsArray[0]);
      this.currencySymbol = symbol;
      this.firstFlag = `currency-flag currency-flag-${flagsArray[0]
        .toString()
        .toLowerCase()}`;
      this.secondFlag = `currency-flag currency-flag-${flagsArray[1]
        .toString()
        .toLowerCase()}`;
    },
    setTimeFrame(timeFrame) {
      this.timeSelection = timeFrame;
    },
    transformArray(response) {
      let chart = response.data.c.map((item, index) => {
        var a = new Date(response.data.t[index] * 1000);
        var months = [
          "Jan",
          "Feb",
          "Mar",
          "Apr",
          "May",
          "Jun",
          "Jul",
          "Aug",
          "Sep",
          "Oct",
          "Nov",
          "Dec",
        ];
        var year = a.getFullYear();
        var month = months[a.getMonth()];
        var date = a.getDate();
        var time = `${date}/${month}/${year}`;
        return {
          x: time,
          y: [
            Number(response.data.o[index].toFixed(2)),
            Number(response.data.h[index].toFixed(2)),
            Number(response.data.l[index].toFixed(2)),
            Number(item.toFixed(2)),
            Number(response.data.v[index].toFixed(2)),
          ],
        };
      });
      const lastItem = chart[chart.length - 1];
      this.price = lastItem.y[3].toFixed(2);
      let change = lastItem.y[3] - lastItem.y[0];
      change = change.toFixed(2);
      change = change < 0 ? change : `+${change}`;
      this.caret = change < 0 ? true : false;
      this.change = change;
      let changePercent = (
        ((lastItem.y[3] - lastItem.y[0]) / lastItem.y[0]) *
        100
      ).toFixed(2);
      changePercent = changePercent < 0 ? changePercent : `+${changePercent}`;
      this.changePercent = `(${changePercent}%)`;
      this.series = [{ data: chart }];
    },
  },
  watch: {
    async exchangeName(val) {
      this.symbolName = "";
      this.firstFlag = "";
      this.secondFlag = "";
      this.price = "";
      this.currencySymbol = "";
      this.change = "";
      this.changePercent = "";
      this.series = [];
      this.caret = null;
      try {
        let response = await axios.get(
          `${process.env.VUE_APP_ENV_API}/forex/symbol?exchange=${val}&token=${process.env.VUE_APP_ENV_TOKEN}`
        );
        this.symbols = response.data;
      } catch (error) {
        // console.log(Object.keys(error), error.message);
      }
    },
    async symbolName(val) {
      if (val) {
        try {
          let response = await axios.get(
            `${process.env.VUE_APP_ENV_API}/forex/candle?symbol=${val.symbol}&resolution=${this.timeSelection}&from=1590988249&to=1591852249&token=${process.env.VUE_APP_ENV_TOKEN}`
          );

          this.transformArray(response);
        } catch (error) {
          // console.log(Object.keys(error), error.message);
        }
      }
    },
    async timeSelection(val) {
      if (this.exchangeName && this.symbolName) {
        try {
          let response = await axios.get(
            `${process.env.VUE_APP_ENV_API}/forex/candle?symbol=${this.symbolName.symbol}&resolution=${val}&from=1590988249&to=1591852249&token=${process.env.VUE_APP_ENV_TOKEN}`
          );

          this.transformArray(response);
        } catch (error) {
          // console.log(Object.keys(error), error.message);
        }
      }
    },
  },
  async created() {
    try {
      let response = await axios.get(
        `${process.env.VUE_APP_ENV_API}//forex/exchange?token=${process.env.VUE_APP_ENV_TOKEN}`
      );
      this.exchanges = response.data;
    } catch (error) {
      console.log(Object.keys(error), error.message);
    }
  },
};
</script>

<style>
@font-face {
  font-family: "Gotham-Book";
  src: local("Gotham-Book"), url(./fonts/Gotham-Book.otf) format("opentype");
}

#app {
  font-family: "Gotham-Book";
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}

*,
body {
  font-family: "Gotham-Book";
}

p {
  margin-bottom: 0 !important;
}
</style>
