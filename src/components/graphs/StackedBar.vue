<script>
import { Bar } from 'vue-chartjs';

export default {
  extends: Bar,
  props: {
    label: {
      type: Array
    },
    chartData: {
      type: Array
    },
  },
  mounted() {
    this.renderBarChart();
  },
  methods:{
    renderBarChart(){
      this.renderChart(
        {
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
        }
      )
    }
  },
  watch: {
    chartData() {
      this.renderBarChart();
    }
  }
};
</script>
