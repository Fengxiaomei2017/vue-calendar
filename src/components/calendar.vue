<template>
  <div class='calen-box'>
    <div class='choice-date'>
      <span>请选择入离时间</span>
      <span><a href='javascript:' @click='dateFinshed'>完成</a></span>
    </div>
    <div class='calen-date'>
      <span>
        <a herf='javascript:' @click='preYear'><<</a>
        <a herf='javascript:' @click='prevMonth'><</a>
      </span>
      <span class='date-time'>{{year}}年{{month}}月</span>
      <span>
        <a herf='javascript:' @click='nextMonth'>></a>
        <a herf='javascript:' @click='nextYear'>>></a>
      </span>
    </div>
    <div class='table-box'>
      <ul class='table-inbox table-th'>
        <li v-for='weekitem in weeks'
            :key='weekitem'>{{weekitem}}</li>
      </ul>
      <ul class='table-inbox table-td'>
        <li v-for='(item,index) in 7'
            v-if='index < week'>
            <span class='none-td'>无</span></li>
        <li v-for='dayitem in days'
            :key='dayitem'>
              <div  v-if='single'
                    class='day-box'
                   :class="[
                    {'activeClass': parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem)) === xBegin}]"
                    @click='mouseEnd(dayitem)'>
                    <span v-if="initYear+''+initMonth+''+dayitem === year+''+month+''+day">今天</span>
                    <span v-else>{{dayitem}}</span>
              </div>
              <div  v-else
                    class='day-box'
                   :class="[
                    {'activeClass': parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem)) === xBegin},
                    {'leactiveClass': parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem)) === xEnd},
                    {'hoverClass': parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem))>xBegin && parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem))<xEnd}]"
                    @click='mouseEnd(dayitem)'>
                    <span v-if="initYear+''+initMonth+''+dayitem === year+''+month+''+day">今天</span>
                    <span v-else-if="initYear+''+initMonth+''+dayitem === year+''+month+''+(day+1)">明天</span>
                    <span v-else>{{dayitem}}</span>
                    <div class='go-home'>入住</div>
                    <div class='leave-home'>离店</div>
              </div>
              <div class='day-box'
                   :class="[{
                    'prevClass': parseInt(year+''+(month<10?'0'+month:month)+''+(dayitem<10?'0'+dayitem:dayitem))
                    <parseInt(initYear+''+(initMonth<10?'0'+initMonth:initMonth)+''+(day<10?'0'+day:day))
                   }]"></div>
        </li>
      </ul>
    </div>
  </div>
</template>
<script>
const weekOptions = ['日', '一', '二', '三', '四', '五', '六']
export default {
  props: ['single'],
  data () {
    return {
      weeks: weekOptions,
      value: '',
      days: '',
      week: '',
      initYear: '',
      initMonth: '',
      year: '',
      month: '',
      day: '',
      xBegin: '',
      xEnd: ''
    }
  },
  created: function () {
    this._init()
  },
  watch: {
    month: function () {
      this.days = this.getDaysInMonth(this.year, this.month)
      this.week = this.getWeekInMounth(this.year, this.month)
    },
    year: function () {
      this.days = this.getDaysInMonth(this.year, this.month)
      this.week = this.getWeekInMounth(this.year, this.month)
    }
  },
  methods: {
    _init: function () {
      this.value = new Date()
      this.initYear = this.value.getFullYear()
      this.initMonth = this.value.getMonth() + 1
      this.year = this.initYear
      this.month = this.initMonth
      this.day = this.value.getDate()
      let month = this.transforDay(this.month)
      this.xBegin = parseInt(this.year + '' + month + '' + this.transforDay(this.day))
      this.xEnd = parseInt(this.year + '' + month + '' + this.transforDay(this.day + 1))
    },
    prevMonth: function () {
      this.month -= 1
      if (this.month < 1) {
        this.year -= 1
        this.month = 12
      }
    },
    nextMonth: function () {
      this.month += 1
      if (this.month > 12) {
        this.year += 1
        this.month = 1
      }
    },
    preYear: function () {
      this.year -= 1
    },
    nextYear: function () {
      this.year += 1
    },
    mouseEnd: function (dayitem) {
      let month
      if (this.single) {
        month = this.transforDay(this.month)
        dayitem = this.transforDay(dayitem)
        this.xBegin = parseInt(this.year + '' + month + '' + dayitem)
      } else {
        if (typeof this.xEnd === 'number' && typeof this.xBegin === 'number') {
          this.xBegin = ''
          this.xEnd = ''
        }
        if (typeof this.xBegin === 'number') {
          month = this.transforDay(this.month)
          dayitem = this.transforDay(dayitem)
          this.xEnd = parseInt(this.year + '' + month + '' + dayitem)
          if (this.xEnd <= this.xBegin) {
            this.xBegin = this.xEnd
            this.xEnd = ''
          }
        } else if (typeof this.xBegin === 'string' || typeof this.xEnd === 'number') {
          month = this.transforDay(this.month)
          dayitem = this.transforDay(dayitem)
          this.xBegin = parseInt(this.year + '' + month + '' + dayitem)
          this.xEnd = ''
        }
      }
    },
    dateFinshed: function () {
      if (this.single) {
        console.log('选择日期：' + this.xBegin)
      } else {
        if (this.xEnd < this.xBegin) {
          return
        }
        console.log('开始日期：' + this.xBegin)
        console.log('结束日期：' + this.xEnd)
      }
    },
    // 根据年份和月份算出本月多少天
    getDaysInMonth: function (year, month) {
      month = parseInt(month, 10)
      var temp = new Date(year, month, 0)
      return temp.getDate()
    },
    // 根据年月日算出星期几
    getWeekInMounth: function (year, month) {
      let newDate = year + '-' + month + '-' + '1'
      var week = new Date(newDate).getDay()
      return week
    },
    // 月份或日期小于10时
    transforDay: function (n) {
      return n < 10 ? '0' + n : n
    }
  }
}
</script>
<style scoped>
  .calen-box {
    padding-top: 10px;
    background-color: #fff;
    border-top: 1px solid #e4e7ed;
  }
  .choice-date,.calen-date {
    width: 100vw;
    display: flex;
  }
  .choice-date {
    margin-bottom: 10px;
  }
  .calen-date {
    border-bottom: 1px solid #e4e7ed;
  }
  .choice-date span, .calen-date span {
    flex: 1;
    height: 30px;
    padding: 0 5px;
    line-height: 30px;
    cursor: pointer;
  }
  .choice-date span:last-child, .calen-date span:last-child {
    text-align: right;
  }
  .choice-date a {
    display: inline-block;
    width: 50px;
    height: 25px;
    line-height: 25px;
    color: #409eff;
    background-color: #fff;
    text-align: center;
    border: 1px solid #409eff;
    border-radius: 4px;
  }
  .calen-date a {
    display: inline-block;
    width: 30px;
  }
  .table-box ul{
    font-size: 0;
    margin: 0;
    padding: 0;
  }
  .table-box .table-inbox {
    width: 100vw;
  }
  .table-box .table-th {
    border-bottom: 1px solid #e4e7ed;
  }
  .table-box .table-inbox li {
    display: inline-block;
    width: 13.8%;
    height: 40px;
    font-size: 14px;
  }
  .table-box .table-th li {
    line-height: 40px;
  }
  .table-box .table-td li {
    position: relative;
    vertical-align: middle;
  }
  .table-box .day-box {
    position: relative;
    height: 35px;
    padding-top: 5px;
    text-align: center;
  }
  .table-box .table-td .none-td {
    opacity: 0;
  }
  .table-box .table-inbox .activeClass, .table-box .table-inbox .leactiveClass {
    width: 100%;
    color: #fff;
    background-color: #409eff;
    border-radius: 2px;
  }
  .table-box .table-inbox .hoverClass {
    width: 100%;
    vertical-align: top;
    background-color: #E2ECFA;
  }
  .table-box .table-inbox .prevClass {
    position: absolute;
    top: 0;
    left: 0;
    z-index: 3;
    width: 100%;
    background-color: rgba(230, 230, 230, 0.5);
  }
  .go-home, .leave-home {
    display: none;
   /* margin-top: 2px;*/
  }
  .table-box .table-inbox .activeClass .go-home,.table-box .table-inbox .leactiveClass .leave-home {
    display: block;
  }
</style>
