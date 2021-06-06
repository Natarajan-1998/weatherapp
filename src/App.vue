<template>
  <div id = "app" :class="typeof weather.main != 'undefined' && weather.main.temp > 25 ? 'hot' : ''">
    <main>
      
      <div class="search-box">
        <input 
          type="text" 
          class="search-bar"
          id="autocomplete" 
          placeholder="Enter City ..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>
      <div class="get-city">
       <button
        type="button"
        class="location-button"
        value="Get Current Location"
        @click="fetchLocation"
        >Get Current Location</button>
        <button
        type="button"
        id="go-button"
        class="location-button"
        @click="fetchWeather">Go</button>

      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">{{ weather.name }}, {{ weather.sys.country }}</div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>
        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">{{ weather.weather[0].description }}</div>
          <div class="feels-like">Feels Like : {{ Math.round(weather.main.feels_like)}}°c</div>
        </div>

      </div>
    </main>
  </div>
</template>

<script>

export default {
  name: 'App',
  data() {
    return{
      api_key: "2685db14222dfebeb28333f520d1e4e7" ,
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      lat:0,
      lng:0
    }
  },
  mounted(){
    new google.maps.places.Autocomplete(
      document.getElementById("autocomplete")
    )
  },
  methods: {
    fetchLocation(){
      //this.query = "Chennai"
      
      const successfulLookup = (position) => {
        const { latitude, longitude} = position.coords;
        fetch(`https://api.opencagedata.com/geocode/v1/json?q=${latitude}+${longitude}&key=a67c77ba25364ce7b58e68e4c543c560`)
        .then(res => {
            return res.json();
          }).then(this.setcity)
          
      }
      navigator.geolocation.getCurrentPosition(successfulLookup,console.log)
    },
    setcity(results){
        this.query = results.results[0].components.city || results.results[0].components.village||results.results[0].components.suburb;
        this.fetchWeather(0);
    },   
    
    fetchWeather(e) {
      if (e.key == "Enter" || e.button == "0"){
        fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}`)
        .then(res => {
            return res.json();
          }).then(this.setResults);
      }
      
    },
    setResults (results) {
      this.weather = results;
      this.query = '';
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

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
body {
  font-family: 'montserrat', sans-serif;
}
#app {
  background-image: url('./assets/cold-bg.jpg');
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}
#app.hot {
  background-image: url('./assets/warm-bg.jpg');
}
main {
  min-height: 100vh;
  padding: 25px;
  background-image: linear-gradient(to bottom, rgba(0, 0, 0, 0.25), rgba(0, 0, 0, 0.75));
}
.get-city .location-button{
  
  border: none;
  color: rgb(78, 64, 64);
  padding: 16px 32px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 16px;
  margin: 4px 2px;
  transition-duration: 0.4s;
  cursor: pointer
}
.location-button:hover {
  background-color: #4CAF50;
  color: white;
}
.search-box {
  width: 100%;
  margin-bottom: 30px;
}
.search-box .search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  
  color: #313131;
  font-size: 20px;
  appearance: none;
  border:none;
  outline: none;
  background: none;
  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}
.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location{
  color: white;
  font-size: 40px;
  font-weight: 1000;
  text-align: left;
  text-shadow: 2px 5px rgba(0, 0, 0, 0.75);
}

.location-box .date{
  color: white;
  font-size: 20px;
  font-weight: 400;
  text-align: left;
  font-style: italic;
}
.weather-box {
  text-align: center;
}
.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #FFF;
  font-size: 102px;
  font-weight: 900;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color:rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .weather {
  color: #FFF;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .feels-like {
  padding: 10px 25px;
  color: #FFF;
  font-size: 20px;
  font-weight: 300;
  font-style: initial;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
