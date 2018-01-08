<template lang="html">
  <div
    class="datepicker"
    :class="{'open': showDatepicker, 'inline': inline}"
    ref="datepicker"
    v-click-outside="outside"
    @keyup.up="focusUp"
    @keyup.right="focusRight"
    @keyup.down="focusDown"
    @keyup.left="focusLeft"
  >
    <div class="toolbar">
      <button type="button" class="toolbar-btn" @click="prevMonth()">&lsaquo;</button>
      <div class="toolbar-select">
        <span>{{ currentYear }}</span>
        <select class="month" v-model="currentMonth" @change="changeMonth">
          <option v-for="(month, i) in monthNames" :value="i" :selected="i = currentMonth">{{month}}</option>
        </select>
      </div>
      <button type="button" class="toolbar-btn" @click="nextMonth()">&rsaquo;</button>
    </div>
    <div class="calendar" ref="calendar">
      <div class="weekdays" v-for="day in dayOrder">
        <abbr :title="dayNames[day]">{{ dayNames[day].substring(0,2) }}</abbr>
      </div>
      <button
        class="date"
        type="button"
        v-for="date in daysInMonth"
        :ref="date.getTime()"
        :disabled="(date.getMonth() !== currentMonth || isDisabled(date) || isPassed(date))"
        @click="selectDay(date)"
        :class="{
          'today': isToday(date),
          'selected': (date.getTime() === selectedDate),
          'few': isFew(date)
        }"
      >
        {{ date.getDate() }}
        <span v-if="isFew(date)" class="few">Få</span>
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
    selected: {
      type: [String, Date],
      required: false
    },
    setFewDates: {
      type: Array,
      required: false
    },
    disablePast: {
      type: Boolean,
      required: false
    },
    show: {
      type: Boolean,
      required: false
    },
    inline: {
      type: Boolean,
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
  computed: {
    showDatepicker () {
      if (!this.inline) {
        return this.show
      }
      return true
    }
  },
  methods: {
    init () {
      let date = new Date().getTime()
      this.today = new Date(new Date(date).getFullYear(), new Date(date).getMonth(), new Date(date).getDate())
      if (this.selected) {
        date = new Date(this.selected).getTime()
      }
      this.selectedDate = new Date(new Date(date).getFullYear(), new Date(date).getMonth(), new Date(date).getDate()).getTime()
      this.currentDate = new Date(date).getDate()
      this.currentMonth = new Date(date).getMonth()
      this.currentYear = new Date(date).getFullYear()
      this.getDaysInMonth(this.currentMonth, this.currentYear)
    },
    getDaysInMonth (month, year) {
      const date = new Date(year, month, 1)
      let days = []
      while (date.getMonth() === month) {
        days.push(new Date(date))
        date.setDate(date.getDate() + 1)
      }
      // Fill out before
      let before = this.padBefore(days)
      days = before.concat(days)
      // Fill out after
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
        padBeforeArray.unshift(new Date(padDate.setDate(padDate.getDate() - 1)))
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
      this.selectedDate = date.getTime()
      if (this.$refs[date.getTime()] && this.$refs[date.getTime()].length > 0) {
        this.$refs[date.getTime()][0].focus()
        this.$emit('date', this.convertDate(date))
      } else {
        var self = this
        setTimeout(function () {
          self.$refs[date.getTime()][0].focus()
          self.$emit('date', self.convertDate(date))
        }, 200)
      }
    },
    isPassed (date) {
      if (this.disablePast) {
        const today = this.today
        if (date.getTime() < today.getTime()) {
          return true
        }
        return false
      }
    },
    isToday (date) {
      const today = this.today
      if (today.getTime() === date.getTime()) {
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
      monthSpan.push(this.convertDate(days[0]))
      monthSpan.push(this.convertDate(days[days.length - 1]))
      this.$emit('month', monthSpan)
    },
    focusUp () {
      this.selectDate(-7)
    },
    focusRight () {
      this.selectDate(1)
    },
    focusDown () {
      this.selectDate(7)
    },
    focusLeft () {
      this.selectDate(-1)
    },
    selectDate (days) {
      let selected = new Date(this.selectedDate)
      let dir = days < 0 ? 'prev' : 'next'
      let navigateTo = this.selectNextPossibleDate(selected, dir, Math.abs(days))
      if (selected.getFullYear() !== navigateTo.getFullYear() || selected.getMonth() !== navigateTo.getMonth()) {
        this.currentDate = new Date(navigateTo).getDate()
        this.currentMonth = new Date(navigateTo).getMonth()
        this.currentYear = new Date(navigateTo).getFullYear()
        this.getDaysInMonth(navigateTo.getMonth(), navigateTo.getFullYear())
      }
      this.selectDay(navigateTo)
    },
    selectNextPossibleDate (date, dir, amount) {
      let current = new Date(date)
      let maxAttempts = 10
      let targetDate = new Date(current)
      if (dir === 'prev') {
        targetDate = new Date(targetDate.setDate(targetDate.getDate() - amount))
      } else if (dir === 'next') {
        targetDate = new Date(targetDate.setDate(targetDate.getDate() + amount))
      }
      while (maxAttempts > 0) {
        if (this.disablePast && this.isPassed(targetDate)) {
          console.warn(`Datepicker: Target date (${targetDate}) has passed`)
          return current
        }
        if (this.isDisabled(targetDate)) {
          if (dir === 'prev') {
            targetDate = new Date(targetDate.setDate(targetDate.getDate() - 1))
          } else if (dir === 'next') {
            targetDate = new Date(targetDate.setDate(targetDate.getDate() + 1))
          }
        } else {
          return targetDate
        }
        maxAttempts--
      }
      return current
    },
    convertDate (date) {
      return date.toLocaleDateString()
    },
    outside: function (e) {
      if (!this.inline && this.show) {
        this.$emit('close', true)
      }
    }
  },
  directives: {
    'click-outside': {
      bind: function (el, binding, vNode) {
        // Provided expression must evaluate to a function.
        if (typeof binding.value !== 'function') {
          const compName = vNode.context.name
          let warn = `[Vue-click-outside:] provided expression '${binding.expression}' is not a function, but has to be`
          if (compName) { warn += `Found in component '${compName}'` }

          console.warn(warn)
        }
        // Define Handler and cache it on the element
        const bubble = binding.modifiers.bubble
        const handler = (e) => {
          if (bubble || (!el.contains(e.target) && el !== e.target)) {
            binding.value(e)
          }
        }
        el.__vueClickOutside__ = handler
        // add Event Listeners
        document.addEventListener('click', handler)
      },
      unbind: function (el, binding) {
        // Remove Event Listeners
        document.removeEventListener('click', el.__vueClickOutside__)
        el.__vueClickOutside__ = null
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.datepicker {
  display: none;
  position: absolute;
  min-width: 250px;
  width: 250px;
  background-color: #fff;
  padding: .2rem;
  z-index: 999;
  box-shadow: 0 0 10px rgba(0,0,0,.2);
  &.open {
    display: block;
  }
  &.inline {
    display: block;
    position: relative;
    width: 100%;
    box-shadow: none;
    z-index: 1;
  }
  .toolbar {
    display: flex;
    justify-content: space-between;
    align-items: center;
    border-bottom: 1px solid #ccc;
    &-btn {
      cursor: pointer;
      font-size: 1.4rem;
      border: 0;
      background-color: transparent;
      padding: .5rem;
      outline: none;
      &:hover, &:focus {
        background-color: #f4f4f4;
      }
      &:active {
        background-color: #eee;
      }
    }
    .month {
      background-image: none;
      -webkit-appearance: none;
      -moz-appearance: none;
      appearance: none;
      border: none;
      background-color: transparent;
      font-size: 1rem;
      outline: none;
      box-shadow: none;
    }
  }
  .calendar {
    display: grid;
    grid-template-columns: repeat(7, 1fr);
    grid-column-gap: 2px;
    grid-row-gap: 2px;
    .weekdays {
      text-align: center;
      padding: .5rem 0;
      abbr {
        text-decoration: none;
        text-transform: uppercase;
        font-size: .8rem;
      }
    }
    .date {
      border: 0;
      padding: 0;
      margin: 0;
      font-size: 1rem;
      outline: none;
      cursor: pointer;
      background-color: #fff;
      border-radius: 4px;
      border: 1px solid transparent;
      display: flex;
      align-items: center;
      justify-content: center;
      flex-direction: column;
      min-height: 40px;
      &:hover, &:focus {
        border-color: #085ca7;
      }
      &.today {
        font-weight: bold;
        background-color: #e3f1fe;
        color: #085ca7;
      }
      &[disabled] {
        color: #999;
      }
      &.few {
        background-color: #ffe6cc;
        color: #000;
      }
      &.selected {
        background-color: #085ca7;
        color: #e3f1fe;
      }
      .few {
        font-size: .8rem;
      }
    }
  }
}
</style>
