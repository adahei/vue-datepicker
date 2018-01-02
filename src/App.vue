<template>
  <div id="app">
    <div>
      <button type="button" @click="openDatepicker">datepicker</button>
      <Datepicker
        :show="show"
        :setDisabledDates="disabled"
        :setFewDates="few"
        @date="selectedDate"
        @month="selectedMonth"
      />
      <p>Selected Date: {{ date }}</p>
      <p>Month: {{ month }}</p>
    </div>
    <hr>
    <div>
      <Datepicker
        :disablePast="true"
        :inline="true"
        :setDisabledDates="disabled"
        :setFewDates="few"
        @date="selectedDate"
        @month="selectedMonth"
      />
      <p>Selected Date: {{ date }}</p>
      <p>Month: {{ month }}</p>
    </div>
  </div>
</template>

<script>
import Datepicker from './components/Datepicker'

export default {
  name: 'app',
  components: {
    Datepicker
  },
  data () {
    return {
      show: false,
      date: null,
      month: null,
      disabled: [
        new Date(2018, 0, 5),
        new Date(2018, 0, 8),
        new Date(2018, 0, 15),
        new Date(2018, 0, 18)
      ],
      few: [
        new Date(2018, 0, 4),
        new Date(2018, 0, 12),
        new Date(2018, 0, 14),
        new Date(2018, 0, 28)
      ]
    }
  },
  methods: {
    openDatepicker () {
      this.show = !this.show
    },
    selectedDate (value) {
      this.date = value
      this.show = false
    },
    selectedMonth (value) {
      this.month = value
    }
  }
}
</script>

<style lang="scss">
* {
  box-sizing: border-box;
  &::before, &::after {
    box-sizing: inherit;
  }
}
html, body {
  margin: 0;
  padding: 0;
}
body {
  font: normal 1rem/1.4 Helvetica, Arial, sans-serif;
}
#app {
  padding: 1rem;
}
</style>
