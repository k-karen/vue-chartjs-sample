<template>
  <div>
    <div style="width: 100%; text-align: center;" class="elevation-1 my-2 px-1 py-1">
      <span>グラフ名:</span> <input style="width: 80%; border: 1px solid #888" v-model="formData.options.title.text">
    </div>
    <table class="elevation-1">
      <tr>
        <th style="width: 30%;">項目名</th>
        <th style="width: 30%;">値</th>
        <th style="width: 20%;">色</th>
        <th style="width: 15%;">削除</th>
      </tr>
      <tr v-for="(item, index) in formData.tableDatas" :key="index">
        <td style="text-align: center;"><input style="width:80%; border: 1px solid #888" v-model="item.label"></td>
        <td style="text-align: center;"><input style="width:80%; border: 1px solid #888" v-model="item.value" type="tel"></td>
        <td style="text-align: center;"><input style="width:80%; border: 1px" v-model="item.color" type="color"></td>
        <td style="text-align: center;"><span class="elevation-1 px-1" @click="deleteFormData(index)">x</span></td>
      </tr>
      <tr>
        <td style="text-align: center;">上限</td>
        <td style="text-align: center;"><input style="width:80%; border: 1px solid #888" v-model="formData.options.scales.yAxes[0].ticks.suggestedMax"></td>
        <td style="text-align: center;">下限</td>
        <td style="text-align: center;"><input style="width:80%; border: 1px solid #888" v-model="formData.options.scales.yAxes[0].ticks.suggestedMin"></td>
      </tr>
    </table>
    <v-btn @click="addDataRow">行を追加する</v-btn>
    <v-btn class="elevation-1">
      <input style="border: 1px" v-model="globalChartColor" type="color">
      <span @click="changeAllColor">統一</span>
    </v-btn>
    <v-btn class="elevation-1">
      <span @click="randomizeAllColor">ランダム</span>
    </v-btn>
  <bar-chart-canvas :data="buildChartData()" :options="buildChartOptions()"/>
  <div style="display:inline-block;" class="elevation-3" @click="saveChartData()">保存</div>
  <select class="elevation-3" v-model="currentStorageKey">
    <option value=null>読込</option>
    <option v-for="storageData in localStorageDataList()" :value="storageData.key" :key="storageData.key" > {{ storageData.name }} </option>
  </select>
  <div style="display:inline-block;" class="elevation-1" @click="chartAsImageBase64()">pngとして表示する</div>
  <div style="display:inline-block;" class="elevation-2" @click="deleteCurrentData()">削除</div>
  <img id="canva-as-image" style="width:100%" src="">
  </div>
</template>

<script>
import BarChartCanvas from '@/components/chart/BarChartCanvas.vue'
const DEFAULT_LABEL = ''
const DEFAULT_VALUE = 0
const DEFAULT_COLOR = '#aaaaaa'
const DEFAULT_TITLE = 'ここにタイトルが入ります'
const BAR_CHART_STORAGE_PREFIX = 'BARCHART'
const STORAGE_TITLE_REGEXP = new RegExp( '^'+ BAR_CHART_STORAGE_PREFIX + '::' + '[0-9a-f]{6}' + '::')

export default {
  components: {
    BarChartCanvas
  },
  data: () => {
    return {
      //フォーム入力データ(グラフ用整形前)
      formData: {
        label: null,
        tableDatas: [
          {
            label: DEFAULT_LABEL,
            value: DEFAULT_VALUE,
            color: DEFAULT_COLOR
          }
        ],
        options: {
          title: {
            display: true,
            text: DEFAULT_TITLE
          },
          scales: {
            yAxes: [{
              ticks: {
                suggestedMax: 100,
                suggestedMin: 0
              }
            }]
          },
          legend: {
            display: false
          }
        }
      },
      //その他
      globalChartColor: DEFAULT_COLOR,
      currentStorageKey: null
    }
  },
  watch: {
    currentStorageKey () {
      if (!this.currentStorageKey || !localStorage.getItem(this.currentStorageKey)) {
        this.formData = this.initFormData()
      } else {
        this.formData = JSON.parse(localStorage.getItem(this.currentStorageKey))
      }
    }
  },
  computed: {
  },
  methods: {
    chartAsImageBase64 () {
      if (!document.getElementById('bar-chart') || !document.getElementById('canva-as-image')) return
      document.getElementById('canva-as-image').src
        = document.getElementById('bar-chart').toDataURL('image/png')
    },
    saveChartData() {
      if(this.currentStorageKey && this.currentStorageKey !== 'null') {
        if(this.currentStorageKey.replace(STORAGE_TITLE_REGEXP,'') === this.formData.options.title.text) {
          localStorage.setItem(this.currentStorageKey, JSON.stringify(this.formData))
        } else {
          const new_key = this.currentStorageKey.match(STORAGE_TITLE_REGEXP)[0] + this.formData.options.title.text
          const old_key = this.currentStorageKey
          localStorage.setItem(new_key, JSON.stringify(this.formData))
          this.currentStorageKey = new_key
          localStorage.removeItem(old_key)
        }
      } else {
        const storageKey = `${BAR_CHART_STORAGE_PREFIX}::${this.generateRandomHash()}::${this.formData.options.title.text}`
        localStorage.setItem(storageKey, JSON.stringify(this.formData))
        this.currentStorageKey = storageKey
      }
    },
    deleteCurrentData () {
      const new_key = null
      const old_key = this.currentStorageKey
      this.formData = this.initFormData()
      localStorage.removeItem(old_key)
      this.currentStorageKey = null
    },
    buildChartData () {
      return {
        labels: this.formData.tableDatas.map(a => a.label),
        datasets: [{
          data: this.formData.tableDatas.map(a => a.value),
          backgroundColor: this.formData.tableDatas.map(a => a.color)
        }]
      }
    },
    buildChartOptions () {
      return this.formData.options
    },
    addDataRow () {
      this.formData.tableDatas.push({
        label: DEFAULT_LABEL,
        value: DEFAULT_VALUE,
        color: this.generateRandomColor()
      })
    },
    generateRandomColor () {
      return `#${this.generateRandomHash()}`
    },
    generateRandomHash () {
      return ("000000" + (Math.random() * 0xFFFFFF | 0).toString(16)).slice(-6)
    },
    deleteFormData (id) {
      var count = 0
      const tempDatas = []
      this.formData.tableDatas.forEach(oneTableData => {
        if (count !== id) tempDatas.push(oneTableData)
        count ++
      })
      this.formData.tableDatas = tempDatas
    },
    changeAllColor () {
      if  (!this.globalChartColor) return
      this.formData.tableDatas.forEach(oneTableData => {
        oneTableData.color = this.globalChartColor
      })
    },
    randomizeAllColor () {
      this.formData.tableDatas.forEach(oneTableData => {
        oneTableData.color = this.generateRandomColor()
      })
    },
    localStorageDataList () {
      var keys = []
      for (var key in localStorage) {
        if (key.match(STORAGE_TITLE_REGEXP)) keys.push({key: key, name: key.replace(STORAGE_TITLE_REGEXP,'')})
      }
      return keys
    },
    initFormData () {
      return {
        label: null,
        tableDatas: [
          {
            label: DEFAULT_LABEL,
            value: DEFAULT_VALUE,
            color: DEFAULT_COLOR
          }
        ],
        options: {
          title: {
            display: true,
            text: DEFAULT_TITLE
          },
          scales: {
            yAxes: [{
              ticks: {
                suggestedMax: 100,
                suggestedMin: 0
              }
            }]
          },
          legend: {
            display: false
          }
        }
      }
    }
  }
}
</script>
<style scoped>
</style>
