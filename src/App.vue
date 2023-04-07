<template>
  <div class="app-in-center">
    <h1 class="text-left">天气数据显示</h1>
    <div class="text-left-align-center">
      <div class="county-input">
      <label for="county">地区：</label>
      <input id="county" type="text" v-model="countyName" class="county-input" />
      </div>
      <button class="search-button" @click="onSearchClicked">Search!</button>
      </div>
    
    <div id="month-avg-temp-chart-container" >
      <LineChart id="month-avg-temp-chart" v-if="searchSuccessed" :data="monthAvgTempArr" :title="titleMonthAvgTemp"></LineChart>
    </div>
    <div id="month-high-low-temp-chart-container">
      <DoubleLineChart id="month-high-low-temp-chart" v-if="searchSuccessed" :data="monthHighTempArr" :data2="monthLowTempArr" :title="titleMonthHighLowTemp"></DoubleLineChart>
    </div>
    <div id="month-rainy-day-container">
      <LineChart id="month-rainy-day" v-if="searchSuccessed" :data="monthAvgRainyDay" :title="titleMonthAvgRainyDay"></LineChart>
    </div>
  </div>
  

</template>

<script>
import { city_data } from './city';
import LineChart from './components/LineChart.vue';
import DoubleLineChart from './components/DoubleLineChart.vue'

export default {
  name: 'App',
  components: {
    LineChart,
    DoubleLineChart,
  },
  data() {
    return {
      areaidMap: new Map(),
      city_data: city_data,
      countyName: "长子",
      needFetch: false,
      areaid: -1,
      weatherJson: Object,
      searchSuccessed: false,
      monthAvgTempArr: [],
      titleMonthAvgTemp: "月平均气温",
      monthHighTempArr: [],
      monthLowTempArr: [],
      titleMonthHighLowTemp: "月最低最高气温",
      monthAvgRainyDay: [],
      titleMonthAvgRainyDay: "月均雨天",
    }
  },
  methods: {
    //if change watched state in a function twice but to the original state finally, the watcher will not
    //be executed. Becuase js is a one-thread framework.
    onSearchClicked() {
      console.log("searching " + this.countyName + "...");
      this.areaid = this.getAreaid(this.countyName);
      if (this.areaid != undefined) {
        this.needFetch = true;
        console.log('areaid is ' + this.areaid);
      }
      else {
        this.needFetch = false;
        console.log('can not find areaid of ' + this.countyName);
      }
    },
    fetchWeatherData() {
      if (this.needFetch === false) {
        return '';
      }
      this.weatherJson = '';
      const areaid = this.areaid;
      console.log('fetch data for id:' + areaid);
      if (areaid != -1) {
        const url = './weatherDatas/';
        try {
          fetch(url + areaid + '.json')
            .then((response) => {
              if (response.ok) {
                const data = response.json();
                //why need return here?
                //so .then executed on the caller--promise
                //if no return, return undefined in defaault.
                return data;
              }
            })
            .then(json => {
              this.weatherJson = json;
              this.searchSuccessed = true;
            });
        } catch (error) {
          console.log(error);
          this.weatherJson = "";
          this.searchSuccessed = false;
        }
      }

      this.needFetch = false;

    },
    parseWeatherData() {
      if(this.searchSuccessed) {
        const areadWeatherData = this.weatherJson[this.areaid];
        var monthAvgTemp = areadWeatherData['yearAvgTemp'];
        this.monthAvgTempArr = Object.values(monthAvgTemp);
        this.monthHighTempArr = Object.values(areadWeatherData['yearMaxTemp']);
        this.monthLowTempArr = Object.values(areadWeatherData['yearMinTemp'])
        this.monthAvgRainyDay = Object.values(areadWeatherData['yearAvgRainDay']);
      }
    },
    fillAreaidMap() {
      //for in iterates properties of object
      console.log(city_data);
      for (let cityName in this.city_data) {
        let city = this.city_data[cityName];
        for (let dityName in city) {
          let dity = city[dityName];
          for (let districtName in dity) {
            let district = dity[districtName];
            this.areaidMap.set(district["NAMECN"], district["AREAID"]);
          }

        }
      }
      console.log(this.areaidMap.size);
    },
    getAreaid(district) {
      return this.areaidMap.get(district);
    }

  },
  watch: {
    searchSuccessed: 'parseWeatherData',
    needFetch: 'fetchWeatherData',
  },
  mounted() {
    this.fillAreaidMap();
  }
}
</script>

<style>

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.county-input {
  font-size: 1.2rem;
  margin-top: 8px;
  margin-bottom: 8px;
}
.search-button {
  font-size: 1.2rem;
  margin-top: 8px;
  margin-bottom: 24px;
  color: rgb(16, 102, 177);
}
.text-left {
  text-align: left;
}
.text-left-align-center {
  text-align: left;
}
.app-in-center {
  display: inline-block;
  align-items: center;
}
</style>
