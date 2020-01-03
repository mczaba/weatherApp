<template>
    <div class="container">
        <div id="main">
          <h2>{{ hour }}</h2>
          <img :src="iconSrc" id="icon">
          <p id="temp">{{ tempCelsius }}°C</p>
        </div>
        <div id="details">
          <h2>Weather details</h2>
          <ul>
              <li>Weather : {{ weather.weather[0].description }}</li>
              <li>Humidity : {{ weather.main.humidity }}%</li>
              <li>Wind : {{ windKmh }}km/h</li>
              <li>Pressure: {{ weather.main.grnd_level }}hPa</li>
              <li>Cloudiness : {{ weather.clouds.all }}%</li>
          </ul>
        </div>
    </div>
</template>

<script>
import moment from 'moment';

export default {
  props: ['weather'],
  computed: {
    hour() {
      const currentDate = new Date(this.weather.dt * 1000);
      return moment(currentDate).format('HH:mm');
    },
    iconSrc() {
      const id = this.weather.weather[0].icon.slice(0, 2);
      if (id === '50') {
        return `http://openweathermap.org/img/wn/${id}d@2x.png`;
      }
      return `./img/${id}.svg`;
    },
    tempCelsius() {
      return Math.round(this.weather.main.temp - 273.5);
    },
    windKmh() {
      return Math.round(this.weather.wind.speed * 36) / 10;
    },
  },
};
</script>

<style lang="scss" scoped>
ul {
    list-style: none;
    li {
      font-size: 18px;
    }
}
.container {
  display: flex;
  border-bottom: 1px solid var(--borders);
  height: 275px;;
  font-family: 'Roboto', sans-serif;
}

#main {
  width: 25%;
  border-right: 1px solid var(--borders);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;

  p {
    font-size:25px;
    margin: 0;
  }

  h2 {
    padding: 0;
  }
}

#details {
  width: calc(75% - 1px);
  h2 {
    text-align: center;
  }
}

#icon {
  width: 80%;
  min-width: 75px;
  max-width: 150px;
}

</style>
