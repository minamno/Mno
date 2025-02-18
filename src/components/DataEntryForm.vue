<v-form @submit.prevent="saveEntry">
    <v-row>
      <v-col cols="12" md="4">
        <v-text-field v-model="entry.hour" label="الساعة" type="number"/>
      </v-col>
      
      <!-- إدخال الحرارات -->
      <v-col cols="12" md="2" v-for="(temp, key) in temperatures" :key="key">
        <v-text-field 
          v-model="entry.temperatures[key]"
          :label="temp.label"
          :rules="[validateTempRange]"
        />
      </v-col>
    </v-row>

    <v-btn type="submit" color="primary">حفظ البيانات</v-btn>
  </v-form>
</template>

<script>
const temperatureRanges = {
  base: [102, 106],
  c1: [85, 88],
  c2: [79, 80],
  heater: [79, 80],
  fusel: [85, 90]
}

export default {
  data: () => ({
    entry: {
      hour: dayjs().hour(),
      temperatures: Object.keys(temperatureRanges).reduce((acc, key) => {
        acc[key] = null
        return acc
      }, {}),
      pressure: null,
      flow: null,
      // ... باقي الحقول
    },
    temperatures: [
      { key: 'base', label: 'القاعدة' },
      { key: 'c1', label: 'C1' },
      { key: 'c2', label: 'C2' },
      { key: 'heater', label: 'السخان' },
      { key: 'fusel', label: 'الفوزيل' }
    ]
  }),
  methods: {
    validateTempRange(value, key) {
      const [min, max] = temperatureRanges[key]
      return value >= min && value <= max || `يجب أن تكون بين ${min}-${max}°C`
    },
    saveEntry() {
      this.$emit('save', this.entry)
      this.resetForm()
    },
    resetForm() {
      this.entry = {/* reset all fields */}
    }
  }
}
</script>
