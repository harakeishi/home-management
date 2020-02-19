<template>
  <div class="home"
    v-loading="loading"
    element-loading-text="Loading..."
    element-loading-spinner="el-icon-loading"
    element-loading-background="rgba(0, 0, 0, 0.8)"
  >
    <table>
      <tr>
        <td width="60%">
          <p>{{value2}}の電力使用状況</p>
          <PowerChart :datacollection="datacollection"></PowerChart>
        </td>
        <td rowspan="2">
        <p>直近の予定</p>
          <el-timeline class='schedule'>
            <el-timeline-item
            v-for="(activity, key, index) in schedule"
            :key="index"
            :timestamp="activity.attributes.start_at"
            color="#02a6a8"
            placement="top"
            >
              <el-card style="background-color: rgba(0, 110, 255, 0.7) ">
                <h2>{{activity.attributes.title}}</h2>
                <p>参加者:
                  <a v-for="(user, keyT, indexT) in activity.relationships.attendees.data" :key="indexT">
                    <a v-if="user.id == '3A_o_7_95JQL,0914432915326698'">真紀</a>
                    <a v-if="user.id == '3A_o_7_95JQL,6308991299285043'">慧士</a>
                  </a>
                </p>
              </el-card>
            </el-timeline-item>
          </el-timeline>
        </td>
      </tr>
      <tr>
        <td><PowerTable :info="info"></PowerTable></td>
      </tr>
      <tr>
        <td>
          <el-radio-group v-model="radio1">
            <el-radio-button label="年"></el-radio-button>
            <el-radio-button label="月"></el-radio-button>
            <el-radio-button label="日"></el-radio-button>
          </el-radio-group>
          <el-date-picker
            v-model="value2"
            :type="pickerType"
            placeholder="Pick a month"
            :format="pickerFormat"
            :value-format="pickerFormat">
          </el-date-picker>
          <el-button @click="today">本日の状況</el-button>
        </td>
        <td>
<!-- weather widget start --><a target="_blank" href="https://booked.jp/weather/kawasaki-w102793"><img src="https://w.bookcdn.com/weather/picture/3_w102793_1_16_293c5c_500_ffffff_333333_08488D_1_ffffff_333333_0_6.png?scode=124&domid=587&anc_id=19763"  alt="booked.net"/></a><!-- weather widget end -->        </td>
      </tr>
    </table>
  </div>
</template>

<script>
// @ is an alias to /src
import axios from 'axios'
import PowerTable from '@/components/PowerTable.vue'
import PowerChart from '@/components/PowerChart.vue'

export default {
  name: 'home',
  data () {
    return {
      info: null,
      datacollection: null,
      loading: true,
      value2: null,
      schedule: null,
      labels: null,
      pickerType: 'month',
      pickerFormat: 'yyyy-MM',
      radio1: '月'
    }
  },
  components: {
    PowerTable,
    PowerChart
  },
  mounted () {
    this.value2 = this.getThisMonth()
    this.getPowerData(this.getThisMonth())
    this.getschedule()
  },
  methods: {
    getThisMonth () {
      var dObj = new Date()
      var y = String(dObj.getFullYear())
      var m = String(100 + dObj.getMonth() + 1).substr(1, 2)
      return y + '-' + m
    },
    getPowerData (inputDate) {
      this.loading = true
      var date = inputDate
      axios.get('http://192.168.10.110:8000/power.php', {
        params: {
          'date': date
        }
      }).then(response => {
        this.info = response.data
        var date = []
        var a = []
        for (let key in this.info.使用量) {
          date.push(this.info.使用量[key].年月日)
          a.push(this.info.使用量[key].ご使用量)
        }
        this.datacollection = {
          labels: date,
          datasets: [
            {
              label: '電気量(kWh)',
              backgroundColor: '#004f50',
              borderColor: '#02a6a8',
              data: a,
              fill: false
            }
          ]
        }
        this.loading = false
      }).catch(error => {
        console.log('--------')
        console.log(error)
        this.loading = false
      })
    },
    today () {
      var dObj = new Date()
      var y = String(dObj.getFullYear())
      var m = String(100 + dObj.getMonth() + 1).substr(1, 2)
      var d = String(100 + dObj.getDate()).substr(1, 2)
      var date = y + '-' + m + '-' + d
      this.value2 = date
      this.pickerType = 'date'
      this.radio1 = '日'
      this.pickerFormat = 'yyyy-MM-dd'
    },
    getschedule () {
      axios.get('https://timetreeapis.com/calendars/3A_o_7_95JQL/upcoming_events', {
        headers: {
          'Accept': 'application/vnd.timetree.v1+json',
          'Authorization': 'Bearer DS7VEYgwLDDZv2MYTH6IQvMtpAk5sc7HcDqPvFE9mmD7WP-M'
        },
        params: {
          timezone: 'Asia/Tokyo',
          days: 7
        }
      }).then((res) => {
        this.schedule = res.data.data
        console.log(this.schedule)
      })
    }
  },
  watch: {
    value2: function (val) {
      this.getPowerData(val)
    },
    radio1: function (val) {
      var dObj = new Date()
      var y = String(dObj.getFullYear())
      var m = String(100 + dObj.getMonth() + 1).substr(1, 2)
      var d = String(100 + dObj.getDate()).substr(1, 2)
      switch (val) {
        case '年':
          console.log(2)
          this.pickerType = 'month'
          this.pickerFormat = 'yyyy'
          this.value2 = y
          break
        case '月':
          console.log(2)
          this.pickerType = 'month'
          this.pickerFormat = 'yyyy-MM'
          this.value2 = y + '-' + m
          break
        case '日':
          console.log(3)
          this.pickerType = 'date'
          this.pickerFormat = 'yyyy-MM-dd'
          this.value2 = y + '-' + m + '-' + d
          break
      }
    }
  }
}
</script>
<style media="screen">
  table{
    width: 100%;
  }
  .schedule{
    overflow-y: auto;
    height: 60vh;
  }
</style>
