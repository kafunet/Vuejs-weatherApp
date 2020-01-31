<template>
    <div id="app">
    <div v-if="errorStr">
    Sorry, but the following error
    occurred: {{errorStr}}
  </div>
  
  <div v-if="gettingLocation">
    <i>Getting your location...</i>
  </div>
  
  <div v-if="location">
    Your location data is {{ location.coords.latitude }}, {{ location.coords.longitude}}
  </div>

    <main>
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar" 
          placeholder="Type your location..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">City: {{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">Today is: {{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">Temperature: {{ Math.round(weather.main.temp) }}&#8451;</div>
          <div class="weather">Weather type: {{ weather.weather[0].main }}</div>
          <div class="windspeed">Wind speed: {{(2.237 * weather.wind.speed).toFixed(1) + " mph"}}</div>
          <span><img :src="getIcon()" alt=""></span>
         
          
        </div>
      </div>
    </main>
  </div>
</template>
<script>
export default {
  name: 'app',
  data () {
    return {
      api_key: '96b6eeda649e5082b2b61231485e4291',
      api: 'https://api.openweathermap.org/data/2.5/',
      iconSrc: 'http://openweathermap.org/img/wn/',
      query: '',
      weather: {},
      location:null,
    gettingLocation: false,
    errorStr:null
    }
  },
  created() {
    //do we support geolocation
    if(!("geolocation" in navigator)) {
      this.errorStr = 'Geolocation is not available.';
      return;
    }

    this.gettingLocation = true;
    // get position
    navigator.geolocation.getCurrentPosition(pos => {
      this.gettingLocation = false;
      this.location = pos;
    }, err => {
      this.gettingLocation = false;
      this.errorStr = err.message;
    })
  },
  
methods: {
fetchWeather () {
      
        fetch(`${this.api}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
          .then(res => {
            return res.json();
          }).then(this.setResults);
      
    },
    setResults (results) {
      return this.weather = results;
    },
    getIcon(){
 return `${this.iconSrc}${this.weather.weather[0].icon}.png`
    },
    dateBuilder () {
      let d = new Date();
      let months = ["January", "February", "March", "April", "May", "June", "July", "August", "September", "October", "November", "December"];
      let days = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    }
  }
}
</script>




<style lang="sass" scoped>

</style>

