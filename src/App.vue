 <v-app>
    <v-main>
      <v-container>
        <v-tabs v-model="tab">
          <v-tab>إدخال البيانات</v-tab>
          <v-tab>لوحة التحكم</v-tab>
          <v-tab>التانكات</v-tab>
        </v-tabs>

        <v-tabs-items v-model="tab">
          <!-- نموذج إدخال البيانات -->
          <v-tab-item>
            <data-entry-form @save="handleDataSave"/>
          </v-tab-item>

          <!-- لوحة التحكم -->
          <v-tab-item>
            <dashboard :shifts="shifts"/>
          </v-tab-item>

          <!-- إدارة التانكات -->
          <v-tab-item>
            <tank-management @transfer="handleTransfer"/>
          </v-tab-item>
        </v-tabs-items>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import DataEntryForm from './components/DataEntryForm'
import Dashboard from './components/Dashboard'
import TankManagement from './components/TankManagement'

export default {
  components: { DataEntryForm, Dashboard, TankManagement },
  data: () => ({
    tab: null,
    shifts: []
  }),
  async mounted() {
    await this.loadData()
  },
  methods: {
    async loadData() {
      this.shifts = await localforage.getItem('shifts') || []
    },
    handleDataSave(newEntry) {
      // حفظ البيانات في التخزين المحلي
      const currentShift = this.getCurrentShift()
      currentShift.hourlyData.push(newEntry)
      this.saveData()
    },
    handleTransfer(transferData) {
      // معالجة نقل التانكات
      const currentShift = this.getCurrentShift()
      currentShift.tankOperations.transfers.push(transferData)
      this.saveData()
    },
    getCurrentShift() {
      const now = dayjs()
      const shiftType = now.hour() >= 9 && now.hour() < 21 ? 'صباحية' : 'مسائية'
      return this.shifts.find(s => 
        dayjs(s.date).isSame(now, 'day') && s.shift === shiftType
      ) || this.createNewShift()
    },
    createNewShift() {
      const newShift = {
        id: dayjs().format('YYYY-MM-DD') + '-' + shiftType,
        date: dayjs().format('YYYY-MM-DD'),
        operator: '',
        hourlyData: [],
        tankOperations: {
          initialST1: 0,
          initialST2: 0,
          transfers: [],
          finalST1: 0,
          finalST2: 0
        }
      }
      this.shifts.push(newShift)
      return newShift
    },
    async saveData() {
      await localforage.setItem('shifts', this.shifts)
    }
  }
}
</script>
