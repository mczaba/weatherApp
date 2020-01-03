<template>
  <div class="container">
    <input
      type="text"
      placeholder="Enter the city followed by the country code if necessary (ex: Paris,fr)"
      v-model="cityInput"
      :class="{trans: trans}"
      @keydown="keyDown"/>
    <button @click="sendData" :class="{trans: trans}">Get Weather</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cityInput: '',
    };
  },
  props: ['trans'],
  methods: {
    sendData() {
      this.$emit('cityGotChosen', this.cityInput);
    },
    keyDown(event) {
      if (event.key === 'Enter') {
        this.sendData();
      }
    },
  },
};
</script>

<style lang="scss" scoped>
  .container {
    height: 35px;
    background-color: var(--background-secondary);
    color: var(--text-color);
    border-radius: 5px;
    font-size: 15px;
    overflow: hidden;
    display: flex;
  }

  input {
        width: calc(100% - 131px);
        height: 100%;
        border: none;
        border-right: 1px solid var(--borders);
        padding-left: 10px;
        background-color: var(--background-secondary);
        color: var(--text-color);
        font-size: 15px;
  }
  input:focus {
    outline: none;
  }

  button {
        width: 130px;
        height: 100%;
        border: none;
        font-size: 15px;
        font-weight: bold;
        background-color: var(--background-secondary);
        color: var(--text-color);
    }
</style>
