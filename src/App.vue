<template>
<div>
    <div class="grid-container" >
      <city-input :class="{inputSmall: weatherDisplay, inputLarge: !weatherDisplay}"
                  @cityGotChosen="getWeather($event)"></city-input>
      <button @click="backwardDay()" v-if="currentDay !== firstDay"
      id="previous-button" class="button-day">{{ previousDay }}</button>
      <transition name="flip" mode="out-in">
        <div id="main-container" v-if="weatherDisplay">
          <div id="flex-header">
            <h2>{{ date }}</h2>
            <h2>{{ city }}</h2>
          </div>
          <current-weather :weather="dayList[currentIndex]" v-if="weatherDisplay"></current-weather>
          <div class="widget-container" :class="{end: currentDay === lastDay}">
            <hour-widget class="widget"
              v-for="(item,index) in dayList"
              :key="index"
              :weather="dayList[index]"
              :index="index"
              :currentIndex="currentIndex"
              @clicked="changeIndex(index)"></hour-widget>
          </div>
        </div>
      </transition>
      <button @click="forwardDay()" v-if="currentDay !== lastDay"
      id="next-button" class="button-day">{{ nextDay }}</button>
      <days-week :current="currentDay" :first="firstDay" :last="lastDay"
                 id="days" v-if="buttonsDisplay"
                 @dayGotChanged="changeDay($event)"></days-week>
      <div id="errorDiv" v-if="errorDisplay">
        <h1 > {{ errorContent }} </h1>
        <p>Enter the name of your city follow by a coma and the
          code of your country (example : 'Paris,fr')</p>
      </div>
    </div>
</div>
</template>

<script>
import moment from 'moment';
import cityInput from './components/CityInput.vue';
import currentWeather from './components/currentWeather.vue';
import hourWidget from './components/hourWidget.vue';
import daysWeek from './components/days.vue';

export default {
  name: 'app',
  data() {
    return {
      buttonsDisplay: false,
      weatherDisplay: false,
      errorDisplay: false,
      errorContent: '',
      weatherList: [],
      currentIndex: 0,
      currentDay: 0,
      firstDay: 0,
      lastDay: 0,
      city: '',
    };
  },
  computed: {
    date() {
      const currentDate = new Date(this.dayList[this.currentIndex].dt * 1000);
      return moment(currentDate).format('dddd, MMMM Do YYYY');
    },
    dayList() {
      return this.weatherList.filter((weather) => {
        const date = new Date(weather.dt * 1000);
        const day = date.getDay();
        if (day === this.currentDay) { return true; }
        return false;
      });
    },
    nextDay() {
      const date = new Date(this.dayList[this.currentIndex].dt * 1000 + 86400000);
      return moment(date).format('dddd');
    },
    previousDay() {
      const date = new Date(this.dayList[this.currentIndex].dt * 1000 - 86400000);
      return moment(date).format('dddd');
    },
  },
  components: {
    'city-input': cityInput,
    'current-weather': currentWeather,
    'hour-widget': hourWidget,
    'days-week': daysWeek,
  },
  methods: {
    getWeather(city) {
      fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=c8f41c6f40b85c060fba58b42ce577ee`, {
        mode: 'cors',
      }).then(response => response.json())
        .then((response) => {
          if (response.list) {
            this.weatherList = [...response.list];
            const date = new Date(response.list[0].dt * 1000);
            this.currentDay = date.getDay();
            this.firstDay = date.getDay();
            const endDate = new Date(response.list[39].dt * 1000);
            this.lastDay = endDate.getDay();
            this.weatherDisplay = true;
            this.buttonsDisplay = true;
            this.errorDisplay = false;
            this.city = `${response.city.name}, ${response.city.country}`;
          } else {
            this.weatherDisplay = false;
            this.errorDisplay = true;
            this.errorContent = response.message;
          }
        })
        .catch((error) => {
          this.weatherDisplay = false;
          this.errorDisplay = true;
          this.errorContent = error;
        });
    },
    changeIndex(index) {
      this.currentIndex = index;
    },
    flip() {
      this.weatherDisplay = false;
      setTimeout(() => {
        this.weatherDisplay = true;
      }, 500);
    },
    forwardDay() {
      this.flip();
      this.currentDay += 1;
      if (this.currentDay === 7) { this.currentDay = 0; }
      this.currentIndex = 0;
    },
    backwardDay() {
      this.flip();
      this.currentDay -= 1;
      if (this.currentDay === -1) { this.currentDay = 6; }
      this.currentIndex = 0;
    },
    changeDay(day) {
      this.flip();
      this.currentDay = day;
      this.currentIndex = 0;
    },
  },
};
</script>

<style lang="scss">
#app {
  font-family: 'Roboto', sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.inputSmall {
  grid-column: 2/3;
  grid-row: 1/2;
}
.inputLarge {
  grid-column: 2/3;
  grid-row: 1/3;
}
#main-container {
  background-color: #4f545c;
  border-radius: 10px;
  color: white;
  overflow: hidden;
  grid-column: 2/3;
  grid-row: 2/3;

  #flex-header {
  display: flex;
  justify-content: space-between;
  border-bottom: 1px solid #36393f;
  grid-column: 1 / 9;
  grid-row: 1/2;
  }
  h2 {
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 12px;
  }
}

.grid-container {
  width: 80%;
  height: 500px;
  margin: 80px auto;
  display: grid;
  grid-template-columns: 1fr 55% 1fr;
  grid-template-rows: 35px 500px 80px;
  grid-column-gap: 10%;
  grid-row-gap: 80px;
}
.button-day {
  background-color: #4f545c83;
  margin: 5em 0 5em 0;
  border: none;
  border-radius: 10px;
  color: white;
  font-size: 33px;
  cursor: pointer;
}
#previous-button {
  grid-column: 1/2;
  grid-row: 2/3;
}

#next-button {
  grid-column: 3/4;
  grid-row: 2/3;
}

#days {
  grid-column: 2/3;
  grid-row: 3/4;
}

.widget-container {
  display: flex;
  justify-content: flex-end;
  background-color: darken(#4f545c, 5%);
}

.end {
  justify-content: flex-start;
}
body {
  background-color: #36393f;
}

@keyframes flip-out {
  from {
    transform: rotateY(0deg);
  }
  to {
    transform: rotateY(90deg);
  }
}

@keyframes flip-in {
  from {
    transform: rotateY(90deg);
  }
  to {
    transform: rotateY(0deg);
  }
}
.flip-enter-active {
  animation: flip-in 0.5s ease-out forwards;
}

.flip-leave-active {
  animation: flip-out 0.5s ease-out forwards;
}

#errorDiv {
  font-family: 'Roboto', sans-serif;
  background-color: #4f545c83;
  border-radius: 10px;
  grid-column: 2/3;
  grid-row: 2/3;
  color: white;
  text-align: center;
  height: 150px;
}
</style>
