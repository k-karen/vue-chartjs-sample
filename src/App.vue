<template>
  <v-app>
    <v-toolbar
      fixed
      app
    >
      <v-toolbar-title center v-text="title" />
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
            sm8
            md6
          >
            <template v-if="!chartType">
              <v-card class="px-3 py-3">
                <select v-model="chartType">
                  <option disabled value=null>Please select graph type</option>
                  <option value="PieChart">円グラフ</option>
                  <option value="BarChart">棒グラフ</option>
                </select>
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


export default {
  components: {
    PieChart, BarChart
  },
  data: () => {
    return {
      fixed: false,
      title: '神グラフジェネレータ',
      chartType: null
    }
  }
}
</script>
