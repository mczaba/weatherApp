<template>
    <ul :class="{trans: trans}">
        <li v-for="(day,index) in filteredWeek" :key="day"
            :class="{five: fiveDays, six: !fiveDays, active: index === currentDay, trans: trans}"
            @click="changeDay($event)">{{ day }}</li>
    </ul>
</template>

<script>
export default {
  props: ['current', 'first', 'last', 'trans'],
  data() {
    return {
      week: ['Sunday', 'Monday', 'Tuesday', 'Wednesday', 'Thursday', 'Friday', 'Saturday'],
    };
  },
  computed: {
    filteredWeek() {
      const filWeek = [];
      let lastDay = this.last;
      if (lastDay < this.first) { lastDay += 7; }
      for (let i = this.first; i <= lastDay; i += 1) {
        if (i >= 7) {
          filWeek.push(this.week[i - 7]);
        } else {
          filWeek.push(this.week[i]);
        }
      }
      return filWeek;
    },
    fiveDays() {
      let lastDay = this.last;
      if (lastDay < this.first) { lastDay += 7; }
      return ((lastDay - this.first) === 4);
    },
    currentDay() {
      let currentDay = this.current;
      if (currentDay < this.first) { currentDay += 7; }
      return (currentDay - this.first);
    },
  },
  methods: {
    changeDay(event) {
      const { target } = event;
      const index = this.week.indexOf(target.textContent);
      this.$emit('dayGotChanged', index);
    },
  },
};
</script>

<style lang="scss" scoped>
    ul {
        margin: 0;
        padding: 0;
        list-style: none;
        display: flex;
        align-items: stretch;
        justify-content: space-around;
        background-color: var(--background-secondary);
        border-radius: 10px;
        overflow: hidden;
    }

    li {
        line-height: 80px;
        text-align: center;
        color: var(--text-color);
        font-family: 'Roboto', sans-serif;
        font-size: 20px;
        cursor: pointer;
    }

    .five {
        width: 20%;
    }
    .six {
        width: 16.66%
    }
    .active {
        background-color: var(--background-secondary-active);
    }
</style>
