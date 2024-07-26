<template>
  <div class="content">
    <!-- 计算平均配速部分 -->
    <el-form label-width="80px" :model="form">
      <el-form-item label="步频">
        <el-input v-model="form.stepFrequency" placeholder="步频（每分钟）"></el-input>
      </el-form-item>
      <el-form-item label="步幅">
        <el-input v-model="form.strideLength" placeholder="步幅（厘米）"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="calculatePace">计算平均配速</el-button>
      </el-form-item>
    </el-form>
    <!-- <div v-if="averagePace">平均配速：{{ averagePace }}</div> -->

    <el-divider></el-divider>

    <!-- 计算完赛时间部分 -->
    <el-form label-width="80px" :model="form">
      <el-form-item>
        <el-switch
          v-model="isFullMarathon"
          active-text="全马"
          inactive-text="半马"></el-switch>
      </el-form-item>
      <el-form-item label="配速">
        <el-input v-model="form.pace" placeholder="请输入每公里配速"></el-input>
      </el-form-item>
      <el-form-item>
        <el-button type="primary" @click="calculateTime">计算完赛时间</el-button>
      </el-form-item>
    </el-form>
    <div v-if="completionTimes.length" class="completion-table">
      <el-table
        :data="completionTimes"
        border
        stripe
        height="300"
        style="width: 300px; margin: 0 auto">
        <el-table-column
          prop="distance"
          label="距离（公里）"
          width="120"></el-table-column>
        <el-table-column prop="time" label="完赛时间"></el-table-column>
      </el-table>
    </div>
  </div>
</template>

<script>
export default {
  name: 'RunningCalculator',
  data() {
    return {
      isFullMarathon: false,
      form: {
        pace: "7'37''",
        stepFrequency: '175',
        strideLength: '75'
      },
      completionTimes: [],
      averagePace: ''
    }
  },
  methods: {
    calculateTime() {
      let distance = this.isFullMarathon ? 42.195 : 21.0975
      let [minutes, seconds] = this.form.pace.split("'").map(Number)
      let totalSeconds = minutes * 60 + seconds
      this.completionTimes = []

      for (let i = 5; i <= distance; i += 5) {
        let totalTimeInSeconds = totalSeconds * i
        let hours = Math.floor(totalTimeInSeconds / 3600)
        let minutesLeft = Math.floor((totalTimeInSeconds % 3600) / 60)
        let secondsLeft = Math.floor(totalTimeInSeconds % 60)
        this.completionTimes.push({
          distance: i,
          time: `${hours}小时${minutesLeft}分钟${secondsLeft}秒`
        })
      }

      // 计算并添加总距离的完赛时间
      let totalTimeInSeconds = totalSeconds * distance
      let hours = Math.floor(totalTimeInSeconds / 3600)
      let minutesLeft = Math.floor((totalTimeInSeconds % 3600) / 60)
      let secondsLeft = Math.floor(totalTimeInSeconds % 60)
      this.completionTimes.push({
        distance: distance,
        time: `${hours}小时${minutesLeft}分钟${secondsLeft}秒`
      })
    },
    calculatePace() {
      let stepFrequency = Number(this.form.stepFrequency)
      let strideLength = Number(this.form.strideLength) / 100 // 转换为米
      let speed = (stepFrequency * strideLength) / 60 // 速度 (米/秒)
      let paceInSeconds = 1000 / speed // 每公里配速 (秒)
      let minutes = Math.floor(paceInSeconds / 60)
      let seconds = Math.floor(paceInSeconds % 60)
      this.averagePace = `${minutes}'${seconds}''`
      this.form.pace = this.averagePace
    }
  }
}
</script>

<style scoped>
.completion-table {
  display: flex;
  justify-content: center;
  width: 100%;
}

.content {
  width: 400px;
  margin: 0 auto;
}
</style>
