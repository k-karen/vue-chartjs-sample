<template>
  <div>
    グラフ名: <input v-model="chartData.datasets[0].label">
    <table>
      <tr>
        <th>項目名</th>
        <th>値</th>
        <th>色</th>
      </tr>
      <tr v-for="n in dataLength" :key="n">
        <td><input v-model="chartData.labels[n-1]"></td>
        <td><input v-model="chartData.datasets[0].data[n-1]"></td>
        <td><input v-model="chartData.datasets[0].backgroundColor[n-1]" type="color"></td>
      </tr>
    </table>
    <button @click="addDataRow">行を追加する</button>
  <v-card>
    <pie-chart-canvas :data="buildChartData()" :options="options" />
  </v-card>
  </div>
</template>

<script>
import PieChartCanvas from '@/components/chart/PieChartCanvas.vue'

export default {
  components: {
    PieChartCanvas
  },
  data: () => {
    return {
      // グラフ描画用データ
      chartData : {
        // ラベル
        labels: [],
        // データ詳細
        datasets: [{
          label: '',
          data: [],
          backgroundColor: []
        }]
      },
      // グラフオプション
      options : {
        title: {
          display: true,
          text: ''
        },
      }
    }
  },
  computed: {
    dataLength () {
      return this.chartData.labels.length
    }
  },
  methods: {
    buildChartData () {
      return {
        labels: this.chartData.labels,
        datasets: [{
          label: this.chartData.datasets[0].label,
          data: this.chartData.datasets[0].data,
          backgroundColor: this.chartData.datasets[0].backgroundColor
        }]
      }
    },
    addDataRow () {
      this.chartData.labels.push('')
      this.chartData.datasets[0].data.push(0)
      this.chartData.datasets[0].backgroundColor.push("rgb(0, 0, 0)")
    }
  }
}
</script>