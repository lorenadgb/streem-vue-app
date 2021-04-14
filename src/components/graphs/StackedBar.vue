<script>
import { Bar, mixins } from 'vue-chartjs';
const { reactiveProp } = mixins;

export default {
  extends: Bar,
  mixins: [reactiveProp],
  props: {
    label: {
      type: Array
    },
    chartData: {
      type: Array
    },
  },
  mounted() {
    this.renderChart({
      labels: this.label,
      datasets: this.chartData
    },
    {
      responsive: true,
      maintainAspectRatio: false,
      scales: {
        xAxes: [{
          stacked: true,
          categoryPercentage: 0.5,
          barPercentage: 1
        }],
        yAxes: [{
          stacked: true
        }]
      }
    })
  }
};
</script>
