<template>
    <div class="container" @click="gotClicked()" :class="{active: index === currentIndex}">
        <h2>{{ hour }}</h2>
        <img :src="iconSrc" id="icon">
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
      const id = this.weather.weather[0].icon.slice(0, 2);
      if (id === '50') {
        return `http://openweathermap.org/img/wn/${id}d@2x.png`;
      }
      return `./img/${id}.svg`;
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
  border-left: 1px solid var(--borders);
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-around;
  cursor: pointer;
  background-color:var(--background-secondary);
  font-family: 'Roboto', sans-serif;

  h2 {
      font-size: 20px;
      margin:15px 0 5px 0;
  }
  p {
    font-size:15px;
  }

}

#icon {
  width: 75px;
}

.container:first-child {
    border: none;
    width: 12.5%;
}

.active {
    background-color: var(--background-secondary-active);
}

@media screen and (max-width: 850px) {
  .container {
    flex-direction: row;
    width: 100%;
    height: 40px;
    border-left: none;
    border-top: 1px solid var(--borders);
  }
  .container:first-child {
    width: 100%;
  }
  #icon {
    width: 50px;
  }
}
</style>
