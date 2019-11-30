<template>
  <div class="container">
    <input type="text" placeholder="Enter the city and country"
      v-model="cityInput" @keydown="keyDown"/>
    <button @click="sendData">Get Weather</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      cityInput: '',
    };
  },
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
    input {
        width: calc(100% - 176px);
        height: 35px;
        margin-right: 30px;
        border-radius: 5px;
        border: none;
        padding-left: 10px;
        font-size: 15px;
        background-color: #4f545c;
        color: white;
    }
    button {
        width: 130px;
        height: 35px;
        border: none;
        border-radius: 5px;
        background-color: #4f545c;
        color: white;
        font-size: 15px;
        font-weight: bold;
    }
</style>
