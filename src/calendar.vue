<template>
  <div class="calendar-warpper">
    <div>
      <slot name="header">
        <div class="calendar-time">{{year}}年{{month}}月</div>
        <!-- <img src="@/resorce/img/file/home_btn_calendar_n@2x.png" alt="" class="calendar-icon"> -->
      </slot>
    </div>
    <!-- <img src="@/resorce/img/file/calenda.png" alt="" width="100%;"> -->
    <div v-if="dataArray.length"> <!--等待数据加载完成再加载swiper-->
      <swiper :options="swiperOption" ref="mySwiper" @slideChange="callback" @slidePrevTransitionEnd = "toPrev" @slideNextTransitionEnd = "toNext">
        <!-- slides -->
        <swiper-slide>
          <ul class="calendar-list">
            <li class="calendar-date" v-for="item in weeks" :key="item">{{item}}</li>
            <li v-for="(item,index) in dataArray" :key="index" class="calendar-date" :class="{'calendar-date-present': item.TPM}">
              <div :class="{'calendar-data-on': isToday(item) }" @click="chooseDay(item)">{{item.data}}</div>
            </li>
          </ul>
        </swiper-slide>
        <swiper-slide>
          <ul class="calendar-list">
            <li class="calendar-date" v-for="item in weeks" :key="item">{{item}}</li>
            <li v-for="(item,index) in dataArray" :key="index" class="calendar-date" :class="{'calendar-date-present': item.TPM}">
              <div :class="{'calendar-data-on': isToday(item) }" @click="chooseDay(item)">{{item.data}}</div>
            </li>
          </ul>
        </swiper-slide>
        <swiper-slide>
          <ul class="calendar-list">
            <li class="calendar-date" v-for="item in weeks" :key="item">{{item}}</li>
            <li v-for="(item,index) in dataArray" :key="index" class="calendar-date" :class="{'calendar-date-present': item.TPM}">
              <div :class="{'calendar-data-on': isToday(item) }" @click="chooseDay(item)">{{item.data}}</div>
            </li>
          </ul>
        </swiper-slide>
      </swiper>
    </div>

  </div>
</template>

<script>
import 'swiper/dist/css/swiper.css'
import { swiper, swiperSlide } from 'vue-awesome-swiper'
export default {
  components: {
    swiper,
    swiperSlide
  },
  computed: {
    swiper () {
      return this.$refs.mySwiper.swiper
    }
  },
  data () {
    return {
      weeks: ['日', '一', '二', '三', '四', '五', '六'],
      year: '',
      month: '',
      day: '',
      dataArray: [],
      swiperOption: {
        observer: true, // 修改swiper自己或子元素时，自动初始化swiper
        observeParents: true, // 修改swiper的父元素时，自动初始化swiper
        loop: true,
        autoplay: false // 可选选项，自动滑动
      }
    }
  },
  props: {
    // 默认选中日期 可以用时间戳或者Date格式 默认为当日
    defaultDate: {
      type: [String, Number],
      required: false
    },
    // 星期的类别 CN为中文 EN为英文
    weekNames: {
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
      this.weekNames === 'CN'
        ? this.weeks = ['日', '一', '二', '三', '四', '五', '六']
        : this.weeks = ['Sun', 'Mon', 'Tues', 'Wed', 'Thur', 'Fri', 'Sat']
    },
    /**
     * 获取今日信息
     * @param year 年份
     * @param month 月份 month介于 0-11 也就是说如果month为7 则为8月
    */
    getToday () {
      let year = ''
      let month = ''
      if (this.defaultDate) {
        year = new Date(this.defaultDate).getFullYear()
        month = new Date(this.defaultDate).getMonth()
        this.day = new Date(this.defaultDate).getDate()
      } else {
        year = new Date().getFullYear()
        month = new Date().getMonth()
        this.day = new Date().getDate()
      }
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
          data: (i + '').replace(/^(\d)$/, '0$1'),
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
          data: (i + 1 + '').replace(/^(\d)$/, '0$1'),
          TPM: false // 是否本月 the present month
        })
      }
      return dataArray
    },
    // 切换到上个月
    toPrev () {
      this.day = ''
      if (this.month === 1) {
        this.year--
        this.month = 12
      } else {
        this.month--
      }
      this.getTimeInfo(this.year, this.month - 1)
    },
    // 切换到下月
    toNext () {
      this.day = ''
      if (this.month === 12) {
        this.year++
        this.month = 1
      } else {
        this.month++
      }
      this.getTimeInfo(this.year, this.month - 1)
    },
    callback () {
      console.log(this.swiper.activeIndex)
    },
    // 判断是否是当天
    isToday (item) {
      if (item.TPM && item.data === this.day.toString()) {
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
  border-top: 1px solid #f5f6fa;
  border-bottom: 1px solid #f5f6fa;
  padding: 18px;
  position: relative;
}
.calendar-time {
  font-size: 18px;
  color: #333;
  text-align: left;
  padding-left: 18px;
  padding-top: 10px;
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
