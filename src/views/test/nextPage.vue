
<template>
  <div>
    <el-table
      :data="tableData"
      border
      style="width: 100%"
    >
      <el-table-column label="序号" prop="id" />
      <el-table-column
        v-for="(item,index) in infoList"
        :key="index"
        :prop="item.prop"
        :label="item.label"
      />
    </el-table>
    <div />
  </div>
  <!--  <div>-->
  <!--    -->
  <!--  </div>-->
</template>

<script>
import moment from 'moment'
export default {
  data() {
    return {
      dayCount: '',
      infoList: [],
      COUNTS: 100,
      // 动态表头数据
      columns: [
        { label: '序号', width: '40', prop: 'name' },
        { label: '农户名称', width: '', prop: 'name' },
        { label: '所属中心店', width: '', prop: 'name' },
        { label: '性别', width: '', prop: 'name' },
        { label: '电话', width: '', prop: 'name' },
        { label: '所在地区', width: '', prop: 'name' },
        { label: '土地面积(亩)', width: '', prop: 'name' },
        { label: '累计购买数量(T)', width: '', prop: 'name' },
        { label: '累计购买金额(元)', width: '', prop: 'name' },
        { label: '是否管理端添加', width: '', prop: 'name' }
      ],
      // 列表数据
      tableData: [
        {
          id: 1,
          name: '中国男',
          shop: '阳光超市',
          sex: '男',
          phone: 18383929383,
          area: '华北',
          acreage: 90,
          quantity: 444,
          money: 34534,
          add: '是'
        }
      ]
    }
  },
  mounted() {
    this.day()
    const today = moment().daysInMonth()
    this.dayCount = today
    // const months = Array.from(range.by('month')).map(
    //   month => month.format('YYYY-MM-DD')
    // )
    console.log(this.dayCount, 'count')
  },
  methods: {
    day() {
      this.columns.forEach(val => {
        const startDate = moment().startOf('month').format('YYYY-MM-DD')
        const endDate = moment().endOf('month').format('YYYY-MM-DD')
        const daysList = []
        const start = moment(startDate)
        const end = moment(endDate)
        const day = end.diff(start, 'days')
        for (let i = 0; i <= day; i++) {
          daysList.push({
            label: moment(startDate).add(i, 'days').format('YYYY年MM月DD日') + '冻结数',
            prop: val.prop
          })
          this.infoList = daysList
        }
        console.log(daysList)
      })
    }
  }
}
</script>
