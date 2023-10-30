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
        @keyup.enter="locationWeather"
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
        address: null,
        weatherDataInfo: null,
        currentLocationData: {},
        currentLatitude: null,
        currentLongitude: null,
      }
  }, 
  created: function() {
   
  },
  mounted(){
    this.$refs.autocomplete.focus();
    let self = this;
    navigator.geolocation.getCurrentPosition(function(position){ 
      self.currentLatitude = position.coords.latitude;
      self.currentLongitude = position.coords.longitude;
      
      var key = '0ec264d8c9018c3ce096a46a309951df';
      const currentLocation = { lat: self.currentLatitude, lng: self.currentLongitude };
      fetch('https://api.openweathermap.org/data/2.5/onecall?lat='+currentLocation.lat+'&lon='+currentLocation.lng+'&appid='+ key).then(function(resp) { return resp.json() }) // Convert data to json
      .then(function(data) {
          let obj = JSON.stringify(data)
          self.weatherDataInfo = JSON.parse(obj)
      })
      .catch(function() {
          // catch any errors
      });
      self.displayLocation(self.currentLatitude, self.currentLongitude);   
	});
  },
  methods: { 
   displayLocation: function(latitude, longitude) {
    return new Promise(function (resolve, reject) {
        var request = new XMLHttpRequest();

        var method = 'GET';
        var url = 'https://maps.googleapis.com/maps/api/geocode/json?latlng=' + latitude + ',' + longitude + '&key=AIzaSyAu05CJD09ia3FIyp73kbokhoAGTRsTfQc&sensor=true';
        var async = true;

        request.open(method, url, async);
        request.onreadystatechange = function () {
            if (request.readyState == 4) {
                if (request.status == 200) {
                    var data = JSON.parse(request.responseText);
                    var address = data.results[0];
                    resolve(address);
                    console.log(address, 'address')
                }
                else {
                    reject(request.status);
                }
            }
        };
        request.send();
    });
},
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
