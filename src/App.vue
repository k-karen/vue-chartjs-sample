<template>
  <v-app>
    <v-toolbar
      fixed
      app
      @click="openChartTypeModal=!openChartTypeModal"
    >
      <v-toolbar-title center v-text="title" />
      <v-menu offset-y>
        <template v-slot:activator="{ on }">
          <v-btn v-on="on">グラフタイプ</v-btn>
        </template>
        <v-list>
          <v-list-tile v-for="(item, index) in chartTypes" :key="index">
            <v-list-tile-title @click="changeChartType(index)">{{ item.name }}</v-list-tile-title>
          </v-list-tile>
        </v-list>
      </v-menu>
      <v-spacer />
    </v-toolbar>
    <v-content>
      <v-container>
        <v-layout
          column
          justify-center
          align-center
        >
          <v-flex
            xs12
            lg12
          >
            <template v-if="!chartType">
              <v-card class="px-3 py-3">
                <v-select
                  v-model="chartType"
                  :items="chartTypes"
                  item-text="name"
                  item-value="value"
                  label='グラフタイプ選択'
                ></v-select>
              </v-card>
            </template>
            <div :is="chartType"></div>
          </v-flex>
        </v-layout>
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
import PieChart from '@/components/chart/PieChart.vue'
import BarChart from '@/components/chart/BarChart.vue'
import LineChart from '@/components/chart/LineChart.vue'


export default {
  components: {
    PieChart, BarChart, LineChart
  },
  data: () => {
    return {
      fixed: false,
      title: '神グラフジェネレータ',
      chartType: null,
      openChartTypeModal: false,
      chartTypes: [
        {
          value: 'PieChart',
          name: '円グラフ'
        },
        {
          value: 'BarChart',
          name: '棒グラフ'
        },
        {
          value: 'LineChart',
          name: '折れ線グラフ'
        }
      ]
    }
  },
  methods: {
    changeChartType (id) {
      this.chartType = this.chartTypes[id].value
    }
  }
}
</script>
