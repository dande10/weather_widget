<template>
  <div id="app">
   <div class="search-location">
      <vue-google-autocomplete
        id="map"
        class="input-field"
        placeholder="Enter Location"
        v-on:placechanged="getAddressData"
        types="(cities)"
        ref="autocomplete"
        @keypress.enter.prevent="locationWeather"
      />
      <button class="btn btn-primary btn-search" @click="locationWeather">
        Search
      </button>
      <button class="btn btn-light btn-cancel m-2" @click="clearLocation">
        X
      </button>
   </div>
    <div class="weather-forecast mt-4" v-if="weatherDataInfo">
      <div class="current-report text-center">
        <h1 v-if="address">{{address.locality}}</h1> 
        <p>{{weatherDataInfo.current.weather[0].main}}</p>
        <h1>{{Math.round(temperatureConverter(weatherDataInfo.current.temp))}}°F</h1>
      </div>
      <table class="table table-borderless" v-for="(item, index) in weatherDataReportToday" :key="index">  
        <tbody>
          <tr>
            <td scope="col"><span>{{getDayText(item)}}</span>
              <span class="p-2">Today</span></td>
            <td scope="col" class="table-text-align">
              <span class="p-2">{{Math.round(temperatureConverter(item.temp.max))}}</span>
              <span class="m-2">{{Math.round(temperatureConverter(item.temp.min))}}</span> 
          </td>
          </tr>
        </tbody>
      </table>
      <div class="line-break m-2"></div>
      <table class="table table-borderless" v-for="(item, index) in weatherDataReport" :key="index">  
        <tbody>
          <tr>
            <td class="col first-col">{{getDayText(item)}}</td>
            <td class="col"><img :src="`https://openweathermap.org/img/wn/${item.weather[0].icon}.png`"/></td>
            <td scope="col" class="table-text-align">
              <span class="p-2">{{Math.round(temperatureConverter(item.temp.max))}}</span>
              <span class="m-2">{{Math.round(temperatureConverter(item.temp.min))}}</span> 
          </td>
          </tr>
        </tbody>
      </table>
      <div class="line-break m-2"></div>
      <div class="weather-describe m-2">
        Today: {{weatherDataReportToday[0].weather[0].main}} currently. The high will be {{Math.round(temperatureConverter(weatherDataReportToday[0].temp.max))}}. 
        {{weatherDataReportToday[0].weather[0].main}} tonight with a low of {{Math.round(temperatureConverter(weatherDataReportToday[0].temp.min))}}.
      </div>
      <div class="line-break m-2"></div>
      <table class="table m-2">  
        <tbody>
          <tr>
            <td scope="col">
              <div>
                SUNRISE
              </div>
              <h2>{{sunRise}}AM</h2>
              </td>
            <td scope="col">
              <div>
                SUNSET
              </div>
              <h2>{{sunSet}}PM</h2>
              </td>
          </tr>
          <tr>
            <td scope="col">
              <div>
                HUMIDITY
              </div>
              <h2>{{weatherDataInfo.current.humidity}}%</h2>
              </td>
            <td scope="col">
              <div>
                FEELS LIKE
              </div>
              <h2>{{Math.round(temperatureConverter(weatherDataInfo.current.feels_like))}}°</h2>
              </td>
          </tr>
        </tbody>
      </table>
    </div>
</div>
</template>

<script>
import VueGoogleAutocomplete from 'vue-google-autocomplete'
// import func from '../vue-temp/vue-editor-bridge';
import moment from 'moment-timezone';
export default {
  components: { VueGoogleAutocomplete },
  data: function () {
      return {
        address: '',
        weatherDataInfo: null
      }
  }, 
  created: function() {
    this.locationWeather();
  },
  computed: {
    weatherDataReportToday(){
      return this.weatherDataInfo.daily.filter((i) => {
        return i;
      }).splice(0, 1)
    },
    weatherDataReport(){
      return this.weatherDataInfo.daily.filter((i) => {
        return i;
      }).splice(1, 7)
    },
    sunRise(){
     const date = new Date(this.weatherDataInfo.current.sunrise * 1000);
     return moment(date, "hmm").format("HH:mm");
   },
   sunSet(){
     const date = new Date(this.weatherDataInfo.current.sunset * 1000);
     return moment(date, "hmm").format("HH:mm");
   }
  },
  methods: {    
   getAddressData: function (addressData) {
    this.address = addressData;
   },
   clearLocation: function(){
     this.$refs.autocomplete.clear();
   },
   locationWeather: function() {
     let self = this;
    var key = '0ec264d8c9018c3ce096a46a309951df';
    var url = this.address ? 'https://api.openweathermap.org/data/2.5/onecall?lat='+this.address.latitude+'&lon='+this.address.longitude+'&appid='+ key: ''
    fetch(url).then(function(resp) { return resp.json() }) // Convert data to json
      .then(function(data) {
        let obj = JSON.stringify(data)
        self.weatherDataInfo = JSON.parse(obj)
      })
      .catch(function() {
        // catch any errors
      });
   },
   temperatureConverter: function(valNum) {
      valNum = parseFloat(valNum);
      return ((valNum-273.15)*1.8)+32;
   },
   getDayText: function(item) {
     const date = new Date(item.dt*1000).getDay();
       switch(date){
         case 0:
           return 'Sunday';
         case 1:
           return 'Monday';
         case 2:
           return 'Tuesday';
         case 3:
           return 'Wednesday';
         case 4:
           return 'Thursday';
         case 5:
           return 'Friday';
         case 6:
           return 'Saturday';
       }     
   }
  }
}
</script>

<style>
body {
  background-color: #20222a !important;
  color: #dadada !important;
  font-family: "Lato",sans-serif !important;
}
.search-location{
  text-align: center; 
  margin-top: 2rem; 
}
.input-field{
  border-bottom: 1px solid #dadada;
  background: none;
  width: 50%;
  margin: 0rem 1rem 0rem 1rem;
  border-right: none;
  color: #dadada;
}
.btn-search {
    background-color: #84b7f4 !important;
    border-color: #84b7f4 !important;
    font-weight: bold !important;
    color: #20222a !important;
}
.today-report{
  display: flex;
}
.line-break {
  border-bottom: 1px solid #dadada;
}
.table{
  color: #dadada !important;
  margin-bottom: 0rem !important;
}
.table-text-align {
  text-align: right;
}
td {
  border-bottom: 1px solid #dadada;
  border-top: none !important;
  vertical-align: middle !important;
}
.first-col{
  width: 55% !important;
}
</style>
