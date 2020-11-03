<template>
    <div class="weather-forecast container mt-4" v-if="weatherDataInfo">
      <div class="current-report text-center">
        <h1>{{address.locality}}</h1> 
        <p>{{weatherDataInfo.current.weather[0].main}}</p>
        <h1>{{Math.round(temperatureConverter(weatherDataInfo.current.temp))}}°F</h1>
      </div>
      <table class="table table-borderless" v-for="(item, index) in weatherDataReportToday" :key="index">  
        <tbody>
          <tr>
            <td scope="col"><span>{{getDayText(item)}}</span>
              <span class="p-2">Today</span></td>
            <td scope="col" class="table-text-align">
              <span class="p-2 maximum">{{Math.round(temperatureConverter(item.temp.max))}}</span>
              <span class="m-2 minimum">{{Math.round(temperatureConverter(item.temp.min))}}</span> 
          </td>
          </tr>
        </tbody>
      </table>
      <div class="line-break m-2"></div>
      <table class="table table-borderless" v-for="(item, id) in weatherDataReport" :key="'A'+ id">  
        <tbody>
          <tr>
            <td class="col first-col">{{getDayText(item)}}</td>
            <td class="col"><img :src="`https://openweathermap.org/img/wn/${item.weather[0].icon}.png`"/></td>
            <td scope="col" class="table-text-align">
              <span class="p-2 maximum">{{Math.round(temperatureConverter(item.temp.max))}}</span>
              <span class="m-2 minimum">{{Math.round(temperatureConverter(item.temp.min))}}</span> 
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
      <table class="table">  
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
</template>
<script>
import moment from 'moment-timezone';
export default {
  name: 'weather-data-widget',
  props:['weatherDataInfo', 'address'],
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
.maximum{
  color: #ff4181;
}
.minimum {
  color: #249ac2;
}
</style>
