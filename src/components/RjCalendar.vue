<template>
    <div class="calendar-container">
      <div class="calendar-header" style="margin-bottom: 10px;">
        <button class="control-btn" style="margin-right: 300px;" @click="toPrevMonth">上一月</button>
        <span>{{year + '年' + (month + 1) + '月' + clickDay + '日'}}</span>
        <button  class="control-btn" style="margin-left : 300px" @click="toNextMonth">下一月</button>
      </div>
      <div style="border : 1px solid #E6E6E6;display: inline-block;">
        <div class="week-container" v-for="(value,index) in data" :key="'week_'+index">
          <button type="button" class="day-container" v-for="(day,idx) in value" :class="[(idx === 0 || idx === 6) ? 'day-container-red' : '','day-' + day,day.indexOf('-') > 0 ? 'btn-disabled' : '',index === 0 ? 'btn-header' : '',(parseInt(day) === today) && (day.indexOf('-') === -1 ) ? 'day-container-active' : '']" :key="'day_'+idx" @click="dayClick(day)" :value="day">{{day.indexOf('-') > 0 ? day.split('-')[0] : day}}</button>
        </div>
      </div>
    </div>
</template>

<script>
    export default {
      name: "RjCalendar",
      data () {
        return {
          day : 1,//当前周几
          today : 1,//当前几号
          year : 0,//当前年份
          month : 0,//当前月份
          clickDay : 0,
          dayForMonth : [],//每月的天数
          data : [['Sun--place','Mon--place','Tue--place','Wed--place','The--place','Fri--place','Sat--place']],//用于构建样式的数据，二维数组
          prevLastDay : 0,//用来记录上个月最后一天是周几
          nextDay : 0,//用来记录下个月第一天是周几
          currentDay : 0,//用来记录当前月份第一天是周几
        }
      },
      watch:{
        today(val){
          this.clickDay = val;
        }
      },
      mounted() {
        this.dataInit();
        this.calculateFirstDay();
        this.buildDayForMonth();
        this.buildData();
      },
      methods : {
        toPrevMonth () {
          this.data = [['Sun--place','Mon--place','Tue--place','Wed--place','The--place','Fri--place','Sat--place']];
          this.year = this.month - 1 < 0 ? this.year - 1 : this.year;
          this.month = this.month - 1 >= 0 ? this.month - 1 : 11;
          this.day = this.prevLastDay;
          this.today = this.dayForMonth[this.month];
          this.calculateFirstDay();
          this.buildDayForMonth();
          this.buildData();
        },
        toNextMonth(){
          this.data = [['Sun--place','Mon--place','Tue--place','Wed--place','The--place','Fri--place','Sat--place']];
          this.year = this.month + 1 > 11 ? this.year + 1: this.year;
          this.month = this.month + 1 > 11 ? 0 : this.month + 1;
          this.day = this.nextDay;
          this.today = 1;
          this.calculateFirstDay();
          this.buildDayForMonth();
          this.buildData();
        },
        dayClick(day){
          var ele = document.getElementsByClassName('day-container-active')[0];
          if(ele){
            ele.classList.remove('day-container-active')
          }
          var eleA = document.getElementsByClassName('day-'+day)[0];
          if(eleA){
            eleA.classList.add('day-container-active')
          }
          this.clickDay = day;
          this.$emit('dayChanged')
        },
        dataInit () {
          var date = new Date();
          this.day = date.getDay();
          this.today = date.getDate();
          this.year = date.getFullYear();
          this.month = date.getMonth();
        },
        //计算月份第一天是周几
        calculateFirstDay(){
          this.currentDay = new Date(this.year + '/' + (this.month + 1) + '/' + '01').getDay();
          this.prevLastDay = this.currentDay - 1 === 0 ? 7 : this.currentDay - 1;
        },
        //计算每个月的天数
        buildDayForMonth(){
          for(var i = 1; i <= 12 ; i++){
            if(i === 1 || i === 3 || i === 5 || i === 7 || i === 8 || i === 10 || i === 12){
              this.dayForMonth.push(31)
            }else if(i === 2){
              this.dayForMonth.push(this.isLeapYear() ? 29 : 28)
            }else {
              this.dayForMonth.push(30)
            }
          }
        },
        buildData(){
          //为了符合实际星期天放在开头
          var tempCData = this.currentDay + 1;

          var nextMonth = false;

          var tempDay = 0;
          for(var i = 0; i < 6; i++){
            var item = [];
            for(var j = tempCData; j <= 7; j++){
              tempDay += 1;
              if(tempDay <= this.dayForMonth[this.month]){
                item.push(nextMonth ? tempDay + '--place' : tempDay + '')
              }else{
                this.nextDay = j - 1;
                tempDay = 1;
                nextMonth = true;
                item.push(tempDay + '--place');
              }
            }
            tempCData = 1;
            this.data.push(item)
          };
          //填充第一行数据
          if(this.data[1].length < 7){
            //上一个月的天数
            var prevDays = this.month - 1 >= 0 ?  this.dayForMonth[this.month - 1] : 31;
            var count = 7 - this.data[1].length;
            for(var i = 0; i < count;i++){
              this.data[1].unshift(prevDays - i+'--place');
            }
          }
        },
        isLeapYear(){
          return (0===this.year%4&&((this.year%100!==0)||(this.year%400===0)));
        }
      }
    }
</script>

<style scoped>
.day-container{
  width: 150px;
  height: 40px;
  background: #ffffff;
  cursor: pointer;
  outline: none;
  border: none;
  border : 1px solid #E6E6E6
}
.day-container:hover{
  background:rgba(64,158,255,0.1);
  border: 1px solid #70b7ff;
  color : #409eff;
}
.control-btn{
  border:1px solid #eee;
  background: #fff;
  padding: 5px 10px;
  -webkit-border-radius: 5px;
  -moz-border-radius: 5px;
  border-radius: 5px;
  cursor: pointer;
  outline: none;
}
  .day-container-red{
    color: red;
  }
  .day-container-active{
    color: #fff;
    background: #409eff;
    border: 1px solid #70b7ff;
  }
.day-container-active:hover{
  color: #fff;
  background: #7bb6ff;
  border: 1px solid #70b7ff;
}
  .btn-disabled{
    pointer-events: none;
    background: #f7f7f7;
  }
  .btn-header{
    background:#fff;
    border-left: none;
    border-right: none;
  }
.btn-header:first-child{
  border-left: 1px solid #E6E6E6;
}
.btn-header:last-child{
  border-right: 1px solid #E6E6E6;
}
</style>
