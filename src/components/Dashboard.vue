<div>
    <v-row>
      <v-col cols="12" md="6">
        <line-chart :data="productionData" title="الإنتاج اليومي"/>
      </v-col>
      <v-col cols="12" md="6">
        <bar-chart :data="tankLevels" title="مستويات التانكات"/>
      </v-col>
    </v-row>

    <v-data-table
      :headers="headers"
      :items="shiftSummary"
      class="elevation-1"
    />
  </div>
</template>

<script>
import { Line, Bar } from 'vue-chartjs'

export default {
  components: { LineChart: Line, BarChart: Bar },
  props: ['shifts'],
  computed: {
    productionData() {
      return {
        labels: this.shifts.map(s => s.date),
        datasets: [{
          label: 'الإنتاج اليومي',
          data: this.shifts.map(s => s.calculations.totalProduction)
        }]
      }
    },
    shiftSummary() {
      return this.shifts.map(shift => ({
        date: shift.date,
        operator: shift.operator,
        totalProduction: shift.calculations.totalProduction,
        averageTemp: this.calculateAverageTemp(shift)
      }))
    }
  },
  methods: {
    calculateAverageTemp(shift) {
      const temps = shift.hourlyData.flatMap(h => Object.values(h.temperatures))
      return (temps.reduce((a,b) => a + b, 0) / temps.length).toFixed(1)
    }
  }
}
</script>
