<template>
  <div class="PowerTable">
    <el-table
     :data="info.使用量"
     height="200"
     :summary-method="getSummaries"
     show-summary
     border
     style="width: 100%">
     <el-table-column
       prop="年月日"
       label="日付"
       width="180">
     </el-table-column>
     <el-table-column
       prop="曜日"
       label="曜日"
       width="180">
     </el-table-column>
     <el-table-column
       prop="契約メニュー"
       label="契約メニュー">
     </el-table-column>
     <el-table-column
       prop="ご使用量"
       label="ご使用量">
       <template slot-scope="scope">
        <a>{{scope.row.ご使用量}}kWh</a>
      </template>
     </el-table-column>
   </el-table>
  </div>
</template>

<script>
export default {
  name: 'PowerTable',
  props: {
    info: Object
  },
  methods: {
    getSummaries (param) {
      const { columns, data } = param
      const sums = []
      columns.forEach((column, index) => {
        if (index === 0) {
          sums[index] = '合計使用量'
          return
        }
        const values = data.map(item => Number(item[column.property]))
        if (!values.every(value => isNaN(value))) {
          sums[index] = values.reduce((prev, curr) => {
            const value = Number(curr)
            if (!isNaN(value)) {
              return prev + curr
            } else {
              return prev
            }
          }, 0)
        } else {
          sums[index] = ''
        }
      })
      sums[1] = Math.ceil(sums[3]) + 'kWh'
      sums[2] = '料金'

      // 電気使用料金計算
      const basicCharge = 796 // 基本料
      const oneStage = 19.88 // 1段料金
      const towStage = 26.46 // 2段料金
      const threeStage = 30.57 // 3段料金
      const adjustment = -2.00 // 燃料費調整額
      const powerGenerationLevy = 2.95 // 再エネ発電賦課金
      const power = Number(sums[3])
      var price = 0
      if (power >= 301) {
        console.log('３段料金')
        price = (basicCharge + (oneStage * 120) + (towStage * 179) + ((power - 300) * threeStage) + (adjustment * power) + (powerGenerationLevy * power)) * 1.1
      } else if (power >= 120) {
        console.log('２段料金')
        price = (basicCharge + (oneStage * 120) + (towStage * (power - 120)) + (adjustment * power) + (powerGenerationLevy * power)) * 1.1
      } else {
        console.log('１段料金')
        price = (basicCharge + (oneStage * power) + (adjustment * power) + (powerGenerationLevy * power)) * 1.1
      }
      sums[3] = Math.ceil(price) + '円'
      return sums
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped lang="scss">

</style>
