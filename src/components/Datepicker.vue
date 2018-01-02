<template lang="html">
  <div class="datepicker">
    <div class="toolbar">
      <button type="button" @click="prevMonth()">Prev</button>
      <div class="toolbar-select">
        <span>{{ currentYear }}</span>
        <select v-model="currentMonth" @change="changeMonth">
          <option v-for="(month, i) in monthNames" :value="i" :selected="i = currentMonth">{{month}}</option>
        </select>
      </div>
      <button type="button" @click="nextMonth()">Next</button>
    </div>
    <div class="calendar">
      <div class="weekdays" v-for="day in dayOrder">
        <abbr :title="dayNames[day]">{{ dayNames[day].substring(0,3) }}</abbr>
      </div>
      <button
        class="date"
        type="button"
        v-for="date in daysInMonth"
        :disabled="(date.getMonth() !== currentMonth || isDisabled(date))"
        @click="selectDay(date)"
        :class="{
          'today': isToday(date),
          'selected': (date === selectedDate),
          'few': isFew(date)
        }"
      >
        {{ date.getDate() }}
      </button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'datepicker',
  props: {
    setDisabledDates: {
      type: Array,
      required: false
    },
    setFewDates: {
      type: Array,
      required: false
    }
  },
  data () {
    return {
      today: null,
      selectedDate: null,
      daysInMonth: [],
      currentDate: null,
      currentMonth: null,
      currentYear: null,
      dayNames: ['söndag', 'måndag', 'tisdag', 'onsdag', 'torsdag', 'fredag', 'lördag'],
      dayOrder: [1, 2, 3, 4, 5, 6, 0],
      monthNames: ['Januari', 'Februari', 'Mars', 'April', 'Maj', 'Juni', 'Juli', 'Augusti', 'September', 'Oktober', 'November', 'December'],
      years: [2017, 2018]
    }
  },
  mounted () {
    this.init()
  },
  methods: {
    init () {
      const date = new Date()
      this.today = date
      this.currentDate = date.getDate()
      this.currentMonth = date.getMonth()
      this.currentYear = date.getFullYear()
      this.getDaysInMonth(this.currentMonth, this.currentYear)
    },
    getDaysInMonth (month, year) {
      const date = new Date(year, month, 1)
      let days = []
      while (date.getMonth() === month) {
        days.push(new Date(date))
        date.setDate(date.getDate() + 1)
      }

      let before = this.padBefore(days)
      days = before.concat(days)

      let after = this.padAfter(days)
      days = days.concat(after)
      this.monthSpan()
      this.daysInMonth = days
    },
    padBefore (dates) {
      let padAmount = dates[0].getDay() === 6 ? 6 : dates[0].getDay() - 1
      let padDate = new Date(dates[0])
      let padBeforeArray = []
      while (padAmount > 0) {
        padBeforeArray.push(new Date(padDate.setDate(padDate.getDate() - 1)))
        padAmount--
      }
      return padBeforeArray
    },
    padAfter (dates) {
      let padAmount = 42 - dates.length
      let padDate = new Date(dates[dates.length - 1])
      let padAfterArray = []
      while (padAmount > 0) {
        padAfterArray.push(new Date(padDate.setDate(padDate.getDate() + 1)))
        padAmount--
      }
      return padAfterArray
    },
    prevMonth () {
      if (this.currentMonth === 0) {
        this.currentMonth = 11
        this.currentYear = this.currentYear - 1
      } else {
        this.currentMonth = this.currentMonth - 1
      }
      this.changeMonth()
    },
    nextMonth () {
      if (this.currentMonth === 11) {
        this.currentMonth = 0
        this.currentYear = this.currentYear + 1
      } else {
        this.currentMonth = this.currentMonth + 1
      }
      this.changeMonth()
    },
    changeMonth () {
      this.getDaysInMonth(this.currentMonth, this.currentYear)
    },
    changeYear () {
      this.getDaysInMonth(this.currentMonth, this.currentYear)
    },
    selectDay (date) {
      this.selectedDate = date
      this.$emit('date', date)
    },
    isToday (date) {
      const today = this.today
      if (
        date !== '' &&
        date.getFullYear() === today.getFullYear() &&
        date.getMonth() === today.getMonth() &&
        date.getDate() === today.getDate()
      ) {
        return true
      }
      return false
    },
    isDisabled (date) {
      // NOTE: dates are object and therefore we cannot use the arr.indexOf()
      if (this.setDisabledDates.map(Number).indexOf(+date) > -1) {
        return true
      }
      return false
    },
    isFew (date) {
      // NOTE: dates are object and therefore we cannot use the arr.indexOf()
      if (this.setFewDates.map(Number).indexOf(+date) > -1) {
        return true
      }
      return false
    },
    monthSpan () {
      const month = this.currentMonth
      const year = this.currentYear
      const date = new Date(year, month, 1)
      let days = []
      while (date.getMonth() === month) {
        days.push(new Date(date))
        date.setDate(date.getDate() + 1)
      }
      let monthSpan = []
      monthSpan.push(days[0])
      monthSpan.push(days[days.length - 1])
      this.$emit('month', monthSpan)
    }
  }
}
</script>

<style lang="scss" scoped>
.datepicker {
  .toolbar {
    display: flex;
    justify-content: space-between;
  }
  .calendar {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    .weekdays {
      text-align: center;
      abbr {
        text-decoration: none;
        text-transform: uppercase;
        font-size: .8rem;
      }
    }
    .date {
      border: 0;
      padding: .5rem 0;
      margin: 0;
      font-size: 1rem;
      outline: none;
      cursor: pointer;
      background-color: #fff;
      &:hover, &:focus {
        background-color: #eee;
      }
      &.today {
        font-weight: bold;
        background-color: #e3f1fe;
        color: #085ca7;
      }
      &[disabled] {
        color: #999;
      }
      &.selected {
        background-color: #085ca7;
        color: #e3f1fe;
      }
      &.few {
        background-color: #ffe6cc;
      }
    }
  }
}
</style>
