<template>
<div :class="mode">
  <nav :class="{trans: trans}">
    <h1>Weather App</h1>
    <div class="toggle-container">
      <img src="/img/moon.png" alt="" width="30px">
      <toggle :mode="mode" @toggle="changeMode()"></toggle>
      <img src="/img/sun.png" alt="" width="30px">
    </div>
  </nav>
  <div class="grid-container" >
    <city-input
      :class="{inputSmall: (errorDisplay || buttonsDisplay),
              inputLarge: !(errorDisplay || buttonsDisplay)}"
      :trans="trans"
      @cityGotChosen="getWeather($event)"
      >
    </city-input>
    <transition  name="fade">
      <button
        @click="backwardDay()"
        v-if="currentDay !== firstDay"
        id="previous-button"
        class="button-day"
        :class="{trans: trans}"
        >{{ previousDay }}</button>
    </transition>
    <transition name="flip" mode="out-in">
      <div id="main-container" v-if="weatherDisplay" :class="{trans: trans}">
        <div id="flex-header">
          <h2>{{ date }}</h2>
          <h2>{{ city }}</h2>
        </div>
        <current-weather :weather="dayList[currentIndex]" v-if="weatherDisplay"></current-weather>
        <div class="widget-container" :class="{end: currentDay === lastDay, trans: trans}">
          <hour-widget class="widget"
            :class="{trans: trans}"
            v-for="(item,index) in dayList"
            :key="index"
            :weather="dayList[index]"
            :index="index"
            :currentIndex="currentIndex"
            @clicked="changeIndex(index)"></hour-widget>
        </div>
      </div>
    </transition>
    <transition  name="fade">
      <button
        @click="forwardDay()"
        v-if="currentDay !== lastDay"
        id="next-button"
        class="button-day"
        :class="{trans: trans}">{{ nextDay }}</button>
    </transition>
    <transition name="fade">
        <days-week
          id="days"
          :current="currentDay"
          :first="firstDay"
          :last="lastDay"
          :trans="trans"
          v-if="buttonsDisplay"
          @dayGotChanged="changeDay($event)">
        </days-week>
    </transition>
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
import toggle from './components/toggle.vue';

export default {
  name: 'app',
  data() {
    return {
      trans: false,
      mode: 'dark',
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
    toggle,
  },
  methods: {
    getWeather(city) {
      fetch(`https://api.openweathermap.org/data/2.5/forecast?q=${city}&APPID=${process.env.VUE_APP_APIKEY}`, {
        mode: 'cors',
      }).then(response => response.json())
        .then((response) => {
          if (response.list) {
            console.log('cc');
            this.weatherList = [...response.list];
            const date = new Date(response.list[0].dt * 1000);
            this.currentDay = date.getDay();
            this.firstDay = date.getDay();
            this.currentIndex = 0;
            const endDate = new Date(response.list[39].dt * 1000);
            this.lastDay = endDate.getDay();
            this.weatherDisplay = true;
            this.buttonsDisplay = true;
            this.errorDisplay = false;
            this.city = `${response.city.name}, ${response.city.country}`;
            this.flip();
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
      if (this.currentDay === this.lastDay) { return; }
      this.flip();
      this.currentDay += 1;
      if (this.currentDay === 7) { this.currentDay = 0; }
      this.currentIndex = 0;
    },
    backwardDay() {
      if (this.currentDay === this.firstDay) { return; }
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
    changeMode() {
      this.trans = true;
      setTimeout(() => {
        this.trans = false;
      }, 500);
      if (this.mode === 'dark') {
        this.mode = 'light';
      } else {
        this.mode = 'dark';
      }
    },
  },
};
</script>

<style lang="scss">


html, body {
    margin: 0;
    padding: 0;
}


.dark {
  --text-color: white;
  --headings-color: white;
  --background-main: #36393f;
  --background-secondary: #4f545c;
  --background-secondary-active: #676d77;
  --background-buttons: #4f545c83;
  --borders: #36393f;
}

.light {
  --text-color: #333333;
  --headings-color: #0077FF;
  --background-main: #FCFCFC;
  --background-secondary: #EBEBEB;
  --background-secondary-active: #d2d2d2;
  --background-buttons: #EBEBEB;
  --borders: #aeaeae;
}

.dark, .light {
  min-height: 100vh;
  margin: 0;
  background-color: var(--background-main);
  transition: background-color 1s ease-in-out;
  font-family: 'Roboto', sans-serif;
}

nav {
  background-color: var(--background-secondary);
  color: var(--text-color);
  height: 60px;
  display: flex;
  justify-content: space-around;
  align-items: center;
  div {
    display: flex;
    align-items: center;

  }
}

.inputSmall {
  grid-column: 2/3;
  grid-row: 1/2;
  transition: transform 0.5s ease-in-out;
}
.inputLarge {
  grid-column: 2/3;
  grid-row: 1/2;
  transform: translateY(300px);
  transition: all 0.5s ease-in-out;
}
#main-container {
  background-color: var(--background-secondary);
  border-radius: 10px;
  color: var(--text-color);
  overflow: hidden;
  grid-column: 2/3;
  grid-row: 2/3;

  #flex-header {
    display: flex;
    justify-content: space-between;
    border-bottom: 1px solid var(--borders);
  }
  h2 {
    font-family: 'Roboto', sans-serif;
    margin: 0;
    padding: 12px;
  }
}

.grid-container {
  width: 80%;
  margin: 50px auto 0 auto;
  display: grid;
  grid-template-columns: 1fr 55% 1fr;
  grid-template-rows: 35px 500px 80px;
  grid-column-gap: 10%;
  grid-row-gap: 80px;
}

.button-day {
  background-color: var(--background-buttons);
  margin: 5em 0 5em 0;
  border: none;
  border-radius: 10px;
  color: var(--text-color);
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
  background-color: var(--background-secondary);
}

.end {
  justify-content: flex-start;
}

@media screen and (max-width: 1600px){
  .grid-container {
    grid-template-columns: 1fr;
    grid-column-gap: 20px;
    grid-row-gap: 50px;
    width: calc(100% - 100px);
  }
  .inputSmall, .inputLarge {
    grid-column:1/2;
  }
  #main-container {
    grid-column: 1/2;
  }
  #days {
   grid-column: 1/2
  }
  .button-day {
    margin: 0;
  }
  #previous-button {
    display: none;
  }
  #next-button {
    display: none;
  }
}

@media screen and (max-width: 850px){
  .grid-container{
    width: calc(100% - 20px);
    grid-template-rows: 35px 1fr 80px;
    grid-template-columns: 1fr 1fr;
    grid-auto-rows: min-content;
    grid-gap: 10px;
    margin: 10px 10px 0 10px;
  }

  #main-container {
    grid-column: 1/3;
  }

  .inputSmall, .inputLarge {
    grid-column:1/3;
  }
  #days {
    display: none;
  }

  #previous-button {
    grid-column: 1/2;
    grid-row: 3/4;
    display: block;
  }

  #next-button {
    grid-column: 2/3;
    grid-row: 3/4;
    display: block;
  }

  .widget-container {
    flex-direction: column;
  }

  .inputLarge{
    transform: none;
  }

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
  background-color: var(--background-secondary);
  border-radius: 10px;
  grid-column: 2/3;
  grid-row: 2/3;
  color: var(--text-color);
  text-align: center;
  height: 150px;
}
.trans {
  transition: all 1s ease-in-out;
}

.fade-enter-active {
  transition: opacity 1s;
}
.fade-leave-active {
  transition: opacity 0.5s;
}
.fade-enter, .fade-leave-to {
  opacity: 0;
}
</style>
