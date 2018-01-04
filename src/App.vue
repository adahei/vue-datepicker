<template>
  <div id="app">
    <div>
      <input
        type="text"
        readonly
        @focus="openDatepicker"
        v-model="date"
      >
      <button type="button" @click="openDatepicker">datepicker</button>
      <Datepicker
        :show="show"
        :setDisabledDates="disabled"
        :setFewDates="few"
        selected="2018-01-24"
        @date="selectedDate"
        @month="selectedMonth"
        @close="closeDatepicker"
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
        selected="2018-02-17"
        @date="selectedDate2"
        @month="selectedMonth2"
      />
      <p>Selected Date: {{ date2 }}</p>
      <p>Month: {{ month2 }}</p>
    </div>
    <hr>

    <table class="table">
      <thead>
        <tr>
          <th>Attributes</th>
          <th>Type</th>
          <th>Info</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>disablePast</td>
          <td>Boolean</td>
          <td>All passed date are disabled</td>
        </tr>
        <tr>
          <td>inline</td>
          <td>Boolean</td>
          <td>Make datepicker inline</td>
        </tr>
        <tr>
          <td>setDisabledDates</td>
          <td>Array</td>
          <td>Array of disabled dates</td>
        </tr>
        <tr>
          <td>setFewDates</td>
          <td>Array</td>
          <td>Array of highligted "few" dates</td>
        </tr>
        <tr>
          <td>selected</td>
          <td>Date</td>
          <td>Date that should be initialy marked as selected</td>
        </tr>
        <tr>
          <td>@date</td>
          <td>Callback</td>
          <td>Returns selected date</td>
        </tr>
        <tr>
          <td>@month</td>
          <td>Callback</td>
          <td>Returns array with first and last day in visible month</td>
        </tr>
        <tr>
          <td>@close</td>
          <td>Callback</td>
          <td>Returns boolean when datepicker is closed</td>
        </tr>
      </tbody>
    </table>

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
      date2: null,
      month2: null,
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
    },
    selectedMonth (value) {
      this.month = value
    },
    selectedDate2 (value) {
      this.date2 = value
    },
    selectedMonth2 (value) {
      this.month2 = value
    },
    closeDatepicker (value) {
      this.show = false
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
.table {
  width: 100%;
  tbody tr td {
    &:first-child {
      font-family: monospace;
      color: red;
    }
    &:nth-child(2) {
      font-family: monospace;
      color: blue;
    }
  }
}
</style>
