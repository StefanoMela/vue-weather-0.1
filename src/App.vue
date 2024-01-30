<script>
import axios from "axios";

export default {
  data() {
    return {
      apiKey: "9bfc080d99108c9d9dcc4f67dbc0e315",
      baseLocationUri: "http://api.openweathermap.org/geo/1.0/direct?q=",
      baseWeatherUri: "https://api.openweathermap.org/data/2.5/weather?lat=",
      keyUri: "&appid=",
      query: "",
      city: {
        lat: 0,
        lon: 0,
      },
      weatherDatas: {
        name: "",
        country: "",
        current: 0,
        felt_temp: 0,
        temp_min: 0,
        temp_max: 0,
        weather: "",
      },
    };
  },
  mounted() {
    this.fetchData();
  },
  methods: {
    async fetchWeather() {
      try {
        const firstResponse = await axios.get(
          this.baseLocationUri + this.query + this.keyUri + this.apiKey
        );

        this.city.lat = firstResponse.data[0].lat;
        this.city.lon = firstResponse.data[0].lon;

        const secondResponse = await axios.get(
          this.baseWeatherUri +
            this.city.lat +
            "&lon=" +
            this.city.lon +
            this.keyUri +
            this.apiKey +
            "&units=metric"
        );

        this.weatherDatas.current = Math.round(secondResponse.data.main.temp);
        this.weatherDatas.felt_temp = Math.round(
          secondResponse.data.main.feels_like
        );
        this.weatherDatas.temp_min = Math.round(
          secondResponse.data.main.temp_max
        );
        this.weatherDatas.temp_max = Math.round(
          secondResponse.data.main.temp_min
        );
        this.weatherDatas.weather = secondResponse.data.weather[0].description;

        this.weatherDatas.name = secondResponse.data.name;
        this.weatherDatas.country = secondResponse.data.sys.country;
      } catch (error) {
        console.error("Error");
      }
    },

    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Sunday",
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
      ];

      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();

      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>

<template>
  <div
    id="app"
    :class="typeof weatherDatas != 'undefined' && weatherDatas.current > 16 ? 'warm' : '' "
  >
    <div class="main container py-5">
      <div class="search-box">
        <input
          type="text"
          name="query"
          id="query"
          class="search-bar"
          placeholder="Search..."
          v-model="query"
          @keyup.enter="fetchWeather"
        />
      </div>
      <div class="weather-wrap">
        <div class="location-box">
          <div class="location" v-if="this.weatherDatas.name">
            {{ this.weatherDatas.name }} - {{ this.weatherDatas.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box" v-if="this.city.lat != 0">
          <span>Temp:</span>
          <div class="temp">{{ this.weatherDatas.current }}°</div>
          <span>Felt:</span>
          <div class="temp">{{ this.weatherDatas.felt_temp }}°</div>
          <span>Min:</span>
          <div class="temp">{{ this.weatherDatas.temp_min }}°</div>
          <span>Max:</span>
          <div class="temp">{{ this.weatherDatas.temp_max }}°</div>
          <span>Weather:</span>
          <div class="temp weather">{{ this.weatherDatas.weather }}</div>
        </div>
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
#app {
  background-image: url("./assets/img/cold-bg.jpg");
  background-size: cover;
  background-position: center;
  transition: 0.4s;
}

#app.warm {
  background-image: url("./assets/img/warm-bg.jpg");
}

main {

  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );

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
  border: none;
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
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box{

  display: flex;
  flex-direction: column;
  align-items: center;

}

.weather-box > .temp {

  text-align: center;
  padding: 10px 25px;
  color: #fff;
  font-size: 62px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}

.weather-box {
  color: #fff;
  font-size: 48px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  text-transform: capitalize;
}
</style>