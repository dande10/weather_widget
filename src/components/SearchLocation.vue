<template>
<div class="search">
   <div class="search-location">
      <vue-google-autocomplete
        id="map"
        class="input-field"
        placeholder="Enter Location"
        v-on:placechanged="getAddressData"
        :enable-geolocation="true"
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
   <weather-data-widget :weatherDataInfo="weatherDataInfo" :address="address" />
</div>   
</template>

<script>
import VueGoogleAutocomplete from 'vue-google-autocomplete';
import WeatherDataWidget from './WeatherDataWidget';
export default {
  components: { VueGoogleAutocomplete, WeatherDataWidget},
  name: 'search-location',
  data: function () {
      return {
        address: '',
        weatherDataInfo: null
      }
  }, 
  created: function() {
    this.locationWeather();
  },
  mounted(){
    this.$refs.autocomplete.focus();
  },
  methods: {    
   getAddressData: function (addressData) {
    this.address = addressData;
   },
   clearLocation: function(){
     this.$refs.autocomplete.clear();
     this.$refs.autocomplete.focus();
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
  }
}
</script>

<style>
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
    background-color: #249ac2 !important;
    border-color: #249ac2 !important;
    font-weight: bold !important;
    color: #000 !important;
}
</style>
