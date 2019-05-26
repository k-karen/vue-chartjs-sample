<script>
import { Line } from 'vue-chartjs'
export default {
  name: 'LineChartCanvas',
  extends: Line,
  props: ['data', 'options'],
  mounted: function(){
      this.renderChart(this.data, this.options)
  },
  watch : {
  	  data : function(val) {
  	  	  this.renderChart(this.data, this.options)
  	  },
  	  options : function(val) {
  	  	  this.renderChart(this.data, this.options)
  	  }
  }
}
</script>