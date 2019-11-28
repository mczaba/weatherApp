<template>
    <div class="container" @click="gotClicked()" :class="{active: index === currentIndex}">
        <h2>{{ hour }}</h2>
        <img :src="iconSrc" id="icon" width="75px">
        <p id="temp">{{ tempCelsius }}°C</p>
    </div>
</template>

<script>
import moment from 'moment';

export default {
  props: ['weather', 'index', 'currentIndex'],
  computed: {
    hour() {
      const currentDate = new Date(this.weather.dt * 1000);
      return moment(currentDate).format('HH:mm');
    },
    iconSrc() {
      return `http://openweathermap.org/img/wn/${this.weather.weather[0].icon}@2x.png`;
    },
    tempCelsius() {
      return Math.round(this.weather.main.temp - 273.5);
    },
  },
  methods: {
    gotClicked() {
      this.$emit('clicked', this.index);
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  width: calc(12.5% - 1px);
  border-left: 1px solid #36393f;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  cursor: pointer;
  background-color:#4f545c;

  h2 {
      font-size: 20px;
      margin:15px 0 5px 0;
  }
  p {
    font-size:15px;
  }

}

.container:first-child {
    border: none;
    width: 12.5%;
}

.active {
    background-color: lighten(#4f545c, 10%);
}
</style>
