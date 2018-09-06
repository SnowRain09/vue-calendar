<template>
  <div class="calendar-warpper">
    <div>
      <slot name="header">
        <div class="calendar-time">{{year}}年{{month}}月</div>
        <!-- <img src="@/resorce/img/file/home_btn_calendar_n@2x.png" alt="" class="calendar-icon"> -->
      </slot>
    </div>
    <!-- <img src="@/resorce/img/file/calenda.png" alt="" width="100%;"> -->
    <ul class="calendar-list">
      <li class="calendar-date" v-for="item in weeks" :key="item">{{item}}</li>
      <v-touch
        tag="li"
        v-for="(item,index) in dataArray"
        :key="index"
        class="calendar-date"
        :class="{'calendar-date-present': item.TPM}"
        @swipeleft="touchLeft"
        @swiperight ="touchRight"
      ><div :class="{'calendar-data-on': isToday(item) }" @click="chooseDay(item)">{{item.data}}</div></v-touch>
    </ul>
  </div>
</template>

<script>
export default {
  data () {
    return {
      weeks: [],
      year: '',
      month: '',
      day: '',
      dataArray: []
    }
  },
  props: {
    defaultDate: {
      type: [String, Array],
      required: false
    },
    weeksType: {
      type: String,
      required: false,
      default: 'CN'
    }
  },
  mounted () {
    // 获取星期类别
    this.getWeeks()
    // 获取初始化日期
    this.getToday()
  },
  methods: {
    getWeeks () {
      this.weeksType === 'CN'
        ? this.weeks = ['日', '一', '二', '三', '四', '五', '六']
        : this.weeks = ['Sun', 'Mon', 'Tues', 'Wed', 'Thur', 'Fri', 'Sat']
    },
    /**
     * 获取今日信息
     * @param year 年份
     * @param month 月份 month介于 0-11 也就是说如果month为7 则为8月
    */
    getToday () {
      let year = new Date().getFullYear()
      let month = new Date().getMonth()
      this.day = new Date().getDate()
      this.year = year
      this.month = month + 1
      this.getTimeInfo(year, month)
    },
    /** 获取日期信息
     * @param currentDays 本月的天数
     * @param startWeekday 开始星期 返回数字 0 为星期日
     * @param endWeekday 结束星期
     * @param daysArray 本月展示部分的时间数组
     * @param nextArray 下个月展示部分的数组
     * @param preArray 上个月份展示部分的数组
     */
    getTimeInfo (year, month) {
      let currentDays = new Date(year, month + 1, 0).getDate()
      let startWeekday = new Date(year, month, 1).getDay()
      let endWeekday = new Date(year, month, currentDays).getDay()
      let daysArray = this.getDaysArray(currentDays)
      let preArray = this.getPreArray(year, month, startWeekday)
      let nextArray = this.getNextArray(endWeekday, preArray.length)
      this.dataArray = []
      this.dataArray = this.dataArray.concat(preArray, daysArray, nextArray)
    },
    // 获取本月时间数组
    getDaysArray (currentDays) {
      let dataArray = []
      for (let i = 1; i <= currentDays; i++) {
        dataArray.push({
          timestamp: new Date(this.year, this.month - 1, i),
          data: i,
          TPM: true // 是否本月 the present month
        })
      }
      return dataArray
    },
    // 获取上月展示部分时间数据
    getPreArray (year, month, startWeekday) {
      let currentDays = new Date(year, month, 0).getDate()
      let preDay = startWeekday === 0 ? 7 : startWeekday
      let dataArray = []
      for (let i = 0; i < preDay; i++) {
        dataArray.unshift({
          timestamp: new Date(this.year, this.month - 2, currentDays),
          data: currentDays,
          TPM: false // 是否本月 the present month
        })
        currentDays--
      }
      return dataArray
    },
    // 获取下月展示部分时间数据
    getNextArray (endWeekday, length) {
      let nextDay = endWeekday === 6 ? 7 : 6 - endWeekday
      nextDay = (nextDay + length) <= 7 ? nextDay + 7 : nextDay
      let dataArray = []
      for (let i = 0; i < nextDay; i++) {
        dataArray.push({
          timestamp: new Date(this.year, this.month, i + 1),
          data: i + 1,
          TPM: false // 是否本月 the present month
        })
      }
      return dataArray
    },
    // 左滑 显示下个月的数据
    touchLeft () {
      this.day = ''
      if (this.month === 12) {
        this.year++
        this.month = 1
      } else {
        this.month++
      }
      this.getTimeInfo(this.year, this.month - 1)
    },
    // 右滑 显示上个月的数据
    touchRight () {
      this.day = ''
      if (this.month === 1) {
        this.year--
        this.month = 12
      } else {
        this.month--
      }
      this.getTimeInfo(this.year, this.month - 1)
    },
    // 判断是否是当天
    isToday (item) {
      if (item.TPM && item.data === this.day) {
        return true
      } else {
        return false
      }
    },
    // 选中日期
    chooseDay (item) {
      if (item.TPM) {
        this.day = item.data
      } else if (item.data > 14) {
        this.touchRight()
        this.day = item.data
      } else {
        this.touchLeft()
        this.day = item.data
      }
      this.$emit('dateChoose', item.timestamp)
    }
  }
}
</script>

<style scoped>
.calendar {
  background-color: #fff;
  border-top: 1px solid #F5F6FA;
  border-bottom: 1px solid #F5F6FA;
  padding: 18px;
  position: relative;
}
.calendar-time {
  font-size: 18px;
  color: #333;
  text-align: left;
  padding-left: 18px;
}
.calendar-icon {
  width: 18px;
  height: 18px;
  position: relative;
  top: 2px;
  margin-left: 8px;
}
.calendar-warpper {
  width: 100%;
  background-color: #fff;
}
.calendar-list {
  background-color: #fff;
  overflow: auto;
  margin-top: 10px;
  padding: 0px;
}
.calendar-date {
  list-style: none;
  float: left;
  width: 14.2%;
  text-align: center;
  font-size: 12px;
  color: #979aa2;
  line-height: 20px;
}
.calendar-date-present {
  color: #333;
}
.calendar-data-on {
  background-color: #3396fa;
  color: #fff;
  width: 20px;
  margin: 0 auto;
  border-radius: 10px;
}
</style>
