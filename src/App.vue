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
          <div class="d-flex flex-row">
            {{ symbolName }}
            {{ price }}
          </div>
          <apexchart
            height="350"
            :options="chartOptions"
            :series="series"
          ></apexchart>
          <TimeFrame @setTimeFrame="setTimeFrame" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import VueApexCharts from "vue3-apexcharts";
import axios from "axios";
import "currency-flags/dist/currency-flags.css";
import Exchanges from "./components/ExchangesOptions.vue";
import Symbols from "./components/SymbolsOptions.vue";
import TimeFrame from "./components/TimeFrame.vue";
import Flags from "./components/Flags.vue";

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
      price: "",
      timeSelection: "D",
      connection: null,
      chartOptions: {
        chart: {
          type: "candlestick",
        },
        title: {
          // text: "Forex Chart",
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
    // eslint-disable-next-line vue/no-unused-components
    Exchanges,
    Symbols,
    TimeFrame,
    Flags,
    apexchart: VueApexCharts,
  },
  methods: {
    onChangeExchange(event) {
      this.exchangeName = event;
    },
    onChangeSymbol(event) {
      this.symbolName = event;
      let flagsArray = this.symbolName.split("/");
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
  },
  watch: {
    // whenever question changes, this function will run
    async exchangeName(val) {
      this.symbolName = "";
      this.firstFlag = "";
      this.secondFlag = "";
      try {
        let response = await axios.get(
          `https://finnhub.io/api/v1/forex/symbol?exchange=${val}&token=sandbox_c8urg4aad3iaocnjlu50`
        );
        this.symbols = response.data;
      } catch (error) {
        // console.log(Object.keys(error), error.message);
      }
    },
    async symbolName(val) {
      console.log(val);
      try {
        let response = await axios.get(
          `https://finnhub.io/api/v1/forex/candle?symbol=${val.symbol}&resolution=${this.timeSelection}&from=1590988249&to=1591852249&token=sandbox_c8urg4aad3iaocnjlu50`
        );
        let chart = response.data.c.map((item, index) => {
          return {
            x: new Date(response.data.t[index]),
            y: [
              Number(response.data.o[index]),
              Number(response.data.h[index]),
              Number(response.data.l[index]),
              Number(item),
              Number(response.data.v[index]),
            ],
          };
        });
        this.series = [{ data: chart }];
      } catch (error) {
        // console.log(Object.keys(error), error.message);
      }

      console.log(this.connection);

      // this.connection.send(
      //   JSON.stringify({
      //     type: "subscribe",
      //     symbol: `${this.exchangeName}:${this.symbolName.displaySymbol}`,
      //   })
      // );

      // this.connection.onmessage = function (event) {
      //   // this.price = event.data.price;
      //   console.log(event);
      // };
    },
    async timeSelection(val) {
      console.log(val);
      try {
        let response = await axios.get(
          `https://finnhub.io/api/v1/forex/candle?symbol=${this.symbolName.symbol}&resolution=${val}&from=1590988249&to=1591852249&token=sandbox_c8urg4aad3iaocnjlu50`
        );
        let chart = response.data.c.map((item, index) => {
          return {
            x: new Date(response.data.t[index]),
            y: [
              Number(response.data.o[index]),
              Number(response.data.h[index]),
              Number(response.data.l[index]),
              Number(item),
              Number(response.data.v[index]),
            ],
          };
        });
        this.series = [{ data: chart }];
      } catch (error) {
        console.log(Object.keys(error), error.message);
      }
    },
  },
  async created() {
    try {
      let response = await axios.get(
        "https://finnhub.io/api/v1//forex/exchange?token=sandbox_c8urg4aad3iaocnjlu50"
      );
      this.exchanges = response.data;
    } catch (error) {
      console.log(Object.keys(error), error.message);
    }

    // this.connection = new WebSocket(
    //   "wss://ws.finnhub.io?token=c8urg4aad3iaocnjlu4g"
    // );

    // this.connection.onopen = function (event) {
    //   console.log(event);
    //   console.log("Successfully connected to the echo websocket server...");
    // };
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
</style>

// https://dev.to/frostqui/how-to-use-axios-with-vue-tips-and-tricks-21e0
//https://www.digitalocean.com/community/tutorials/vuejs-rest-api-axios
//https://v2.vuejs.org/v2/cookbook/using-axios-to-consume-apis.html?redirect=true
//https://lukashermann.dev/writing/how-to-use-async-await-with-vuejs-components/
