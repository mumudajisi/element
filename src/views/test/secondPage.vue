<!--<template>-->
<!--  <div>1111</div>-->
<!--</template>-->
<!--<script>-->
<!--import moment from 'moment'-->
<!--import { extendMoment } from 'moment-range'-->

<!--export default {-->
<!--  data() {-->
<!--    return {-->

<!--    }-->
<!--  },-->
<!--  mounted() {-->
<!--    const mn = extendMoment(moment)-->
<!--    this.getList()-->
<!--  },-->
<!--  methods: {-->
<!--    const enumerateDaysBetweenDates = (startDate, endDate) => {-->
<!--      let daysList = [];-->
<!--      const start = moment(startDate);-->
<!--      const end = moment(endDate);-->
<!--      const day = end.diff(start, "year");-->
<!--      daysList.push(start.format("YYYY"));-->
<!--      for (let i = 1; i <= day; i++) {-->
<!--        daysList.push(start.add(1, "year").format("YYYY"));-->
<!--      }-->
<!--      return daysList;-->
<!--    };-->

<!--    enumerateDaysBetweenDates('2001','2005'); //['2001', '2002', '2003', '2004', '2005']-->
<!--    getList() {-->
<!--      const startDate = moment().startOf('month')-->
<!--      const endDate = moment().startOf('month')-->
<!--      const daysList = []-->
<!--      const SDate = moment(startDate)-->
<!--      const EDate = moment(endDate)-->
<!--      let xt-->
<!--      daysList.push(SDate.format('MM-DD dddd'))-->
<!--      while (SDate.add(1, 'days').isBefore(EDate)) { // 注意这里add方法处理后SDate对象已经改变。-->
<!--        daysList.push(SDate.format('MM-DD dddd'))-->
<!--      }-->
<!--      daysList.push(EDate.format('MM-DD dddd'))-->
<!--      console.log(daysList)-->
<!--      return daysList-->
<!--    }-->
<!--  }-->
<!--}-->
<!--</script>-->
