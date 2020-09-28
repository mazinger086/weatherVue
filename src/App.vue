<template>
  <div
    id="app"
    :class="
      typeof weather.main != 'undefined' && weather.main.temp > 16 ? 'warm' : ''
    "
  >
    <main>
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-wrap" v-if="typeof weather.main != 'undefined'">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">{{ Math.round(weather.main.temp) }}°c</div>
          <div class="weather">
            {{ weather.weather[0].main }}
            <img
              :src="`http://openweathermap.org/img/wn/${weather.weather[0].icon}.png`"
              :alt="weather.weather[0].main"
            />
            
          </div>
        </div>
      </div>
    </main>
  </div>
</template>

<script>


export default {

  
  name: 'App',
  data() {
    return {
      api_key: '5807fc86335e80dade275ed95d497fd3',
      url_base: 'https://api.openweathermap.org/data/2.5/',
      query: '',
      weather: {},
      gettingLocation: false,
      location: ''
    
    }
  },
  methods: {
      fetchWeather(e){
        if(e.key === 'Enter'){
          fetch(`${this.url_base}weather?q=${this.query}&units=metric&APPID=${this.api_key}&lang=es`)
          .then(res=> res.json())
          .then(this.setResults);
        }
      },      

      setResults(results){
        this.weather = results;      
        // console.log(results);
      },
      dateBuilder (){
        let d = new Date();
        let months = ['Enero','Febrero', 'Marzo','Abril','Mayo','Junio','Julio','Agosto','Septiembre','Octubre','Noviembre','Diciembre'];
        let days = ['Domingo','Lunes','Martes','Miercoles','Jueves','Viernes','Sabado'];

        let day = days[d.getDay()];
        let date = d.getDate();
        let month = months[d.getMonth()];
        let year = d.getFullYear();

        return `${day} ${date} ${month} ${year}`
      }      
      
  },
  created() {
      if(!("geolocation" in navigator)){
        this.errorStr= 'Geolocalizacion no soportada';
        return;
      }
      this.gettingLocation = true;
      navigator.geolocation.getCurrentPosition(pos =>{
        this.gettingLocation = false;
        this.location = pos
        
        fetch(`${this.url_base}weather?lat=${pos.coords.latitude}&lon=${pos.coords.longitude}&units=metric&APPID=${this.api_key}&lang=es`)
         .then(res=> res.json())
         .then(this.setResults);

        
      }),
      err => {
        this.gettingLocation = false;
        this.errorStr = err.message
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
  font-family: "Montserrat", sans-serif;
}

#app {
  background-image: url("https://cdn130.picsart.com/286509333009201.gif");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

#app.warm {
  background-image: url("https://s3.amazonaws.com/arc-wordpress-client-uploads/infobae-wp/wp-content/uploads/2019/07/26000146/calor-en-las-vegas.jpg");
}

main {
  min-height: 100vh;
  padding: 25px;
}

.search-box {
  width: 100%;
  margin-bottom: 30px;
}

.search-box,
.search-bar {
  display: block;
  width: 100%;
  padding: 15px;

  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0 16px 0;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box .date {
  color: #fff;
  font-size: 20px;
  font-weight: 500;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 102px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box .weather {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
</style>
