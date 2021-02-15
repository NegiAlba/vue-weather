<template>
  <main>
    <div class="container">
      <div class="sidebar">
        <img src="./assets/logo.png" alt="Meteo logo">
        <form class="search-location" @submit.prevent="getWeather">
          <input type="text" placeholder ="Type in a city" v-model="citySearch" autocomplete="off"/>
        </form>
      </div>
      <div class="content" :class="isDay ? 'day':'night'">
        <transition name="switch" mode="out-in">
          <div v-if="searchResult">
            <header class="localisation-info">
              <h1 class="country-info">{{weather.cityName}}</h1>
              <p class="country-info">{{weather.country}}</p>
            </header>
            <section class="details-info">
              <h2> {{weather.temperature}}°C</h2>
              <p>The lowest temperature for today is {{weather.lowTemp}}°C</p>
              <p>The highest temperature for today is {{weather.highTemp}}°C</p>
              <p>Humidity is at {{weather.humidity}} %</p>
            </section>
            <footer class="description-info">
              <p> {{weather.description}}</p>
              <p>{{weather.feelsLike}}</p>
            </footer>
          </div>
          <div v-else>
            <h3 class="header">City not found</h3>
          </div>
        </transition>
      </div>
    </div>
  </main>
</template>

<script>

export default {
  name: 'App',
  components: {
  },
  data () {
    return {
      isDay : true,
      searchResult:false,
      citySearch: "",
      weather : {
        cityName : "Bordeaux",
        country : "FR",
        temperature : 9,
        description : "Cloudy with a chance of meatballs",
        lowTemp: 0,
        highTemp : 15,
        feelsLike: "Death",
        humidity : 55
      },
    }
  },
  methods : {
    getWeather: async function () {
      console.log(this.citySearch);
      const key = "d787f37416c2a2a82620b9a6d0c619a9";
      const callURL = `http://api.openweathermap.org/data/2.5/weather?q=${this.citySearch}&appid=${key}&units=metric`;

      //? Appel à l'API avec un await dans un try
      try {
        const response = await fetch(callURL);
        const data = await response.json(response);
        console.log(data);
        this.citySearch = "";
        this.weather.cityName = data.name;
        this.weather.country = data.sys.country;
        this.weather.temperature = Math.round(data.main.temp);
        this.weather.description = data.weather[0].description;
        this.weather.lowTemp = Math.round(data.main.temp_min);
        this.weather.highTemp = Math.round(data.main.temp_max);
        this.weather.feelsLike = Math.round(data.main.feels_like);
        this.weather.humidity = Math.round(data.main.humidity);

        this.searchResult = true;

        //? Check if it's daytime or nighttime
        const dayNight = data.weather[0].icon;

        if(dayNight.includes("d")){
          this.isDay = true;
        }else{
          this.isDay = false;
        }

      } catch (error) {
        console.log(error);
        this.searchResult = false;
      }
    }
  }
}
</script>

<style>

img{
  width:200px;
}

.container{
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: grid;
  grid-template-columns: minmax(150px,25%) 1fr;
  padding:0;
  margin:0;
  box-sizing: border-box;
}
.sidebar{
  height:100vh;
  background:#555;
  font-size:2rem;
  text-align:center;
  border-radius: 5px;
}
.content{
  padding:2rem;
  display:grid;
  grid-template-rows: auto 1fr auto;
}

.country-info{
  display: inline-block;
  margin:0rem 0.5rem;
}

  /* switch transitions */
  .switch-enter-from,
  .switch-leave-to {
    opacity: 0;
    transform: translateY(20px);
  }
  .switch-enter-active{
    transition: all 0.5s ease;
  }
  .switch-leave-active {
    transition: all 0.5s ease;
    width: 100%;
  }
</style>
