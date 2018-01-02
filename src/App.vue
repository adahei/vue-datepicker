<template>
  <div id="app">
    <Datepicker
      :setDisabledDates="disabled"
      :setFewDates="few"
      @date="selectedDate"
      @month="selectedMonth"
    />
    <p>Selected Date: {{ date }}</p>
    <p>Month: {{ month }}</p>
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
    selectedDate (value) {
      this.date = value
    },
    selectedMonth (value) {
      this.month = value
    }
  }
}
</script>

<style>
</style>
