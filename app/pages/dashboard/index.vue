<template>
  <div>
    <h1>Dashboard</h1>
    
    <v-row class="mt-4">
      <v-col cols="12" md="3">
        <v-card class="pa-4" color="blue-lighten-5">
          <div class="text-h6 text-medium-emphasis">Total Items</div>
          <div class="text-h3 font-weight-bold">{{ stats.items }}</div>
          <v-btn color="blue" variant="text" @click="navigateTo('/items')" prepend-icon="mdi-eye">
            View Items
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4" color="green-lighten-5">
          <div class="text-h6 text-medium-emphasis">Total Members</div>
          <div class="text-h3 font-weight-bold">{{ stats.members }}</div>
          <v-btn color="green" variant="text" @click="navigateTo('/members')" prepend-icon="mdi-eye">
            View Members
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4" color="orange-lighten-5">
          <div class="text-h6 text-medium-emphasis">Transactions</div>
          <div class="text-h3 font-weight-bold">{{ stats.transactions }}</div>
          <v-btn color="orange" variant="text" @click="navigateTo('/transactions')" prepend-icon="mdi-eye">
            View Transactions
          </v-btn>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="3">
        <v-card class="pa-4" color="red-lighten-5">
          <div class="text-h6 text-medium-emphasis">Total Revenue</div>
          <div class="text-h3 font-weight-bold">${{ stats.revenue }}</div>
          <v-btn color="red" variant="text" @click="refreshStats" prepend-icon="mdi-refresh">
            Refresh
          </v-btn>
        </v-card>
      </v-col>
    </v-row>
    
    <!-- Recent Data Tables -->
    <v-row class="mt-6">
      <v-col cols="12" md="6">
        <v-card>
          <v-card-title class="d-flex justify-space-between align-center">
            <span>Recent Items</span>
            <v-btn variant="text" color="primary" @click="navigateTo('/items')" size="small">
              View All
            </v-btn>
          </v-card-title>
          <v-card-text>
            <v-data-table 
              :items="recentItems" 
              :headers="itemHeaders"
              :loading="loading"
              density="compact"
            >
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
      
      <v-col cols="12" md="6">
        <v-card>
          <v-card-title class="d-flex justify-space-between align-center">
            <span>Recent Transactions</span>
            <v-btn variant="text" color="primary" @click="navigateTo('/transactions')" size="small">
              View All
            </v-btn>
          </v-card-title>
          <v-card-text>
            <v-data-table 
              :items="recentTransactions" 
              :headers="transactionHeaders"
              :loading="loading"
              density="compact"
            >
              <template v-slot:[`item.amount`]="{ item }">
                ${{ item.amount }}
              </template>
            </v-data-table>
          </v-card-text>
        </v-card>
      </v-col>
    </v-row>
    
    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<script setup>
const config = useRuntimeConfig()
const router = useRouter()

const stats = ref({
  items: 0,
  members: 0,
  transactions: 0,
  revenue: 0
})

const recentItems = ref([])
const recentTransactions = ref([])
const loading = ref(false)
const snackbar = ref(false)
const snackbarColor = ref('')
const snackbarText = ref('')

const itemHeaders = [
  { title: 'Name', key: 'name' },
  { title: 'Price', key: 'price' },
  { title: 'Quantity', key: 'quantity' }
]

const transactionHeaders = [
  { title: 'Type', key: 'type' },
  { title: 'Amount', key: 'amount' },
  { title: 'Status', key: 'stat' },
  { title: 'Date', key: 'date' }
]

const loadDashboardData = async () => {
  loading.value = true
  try {
    // Load counts
    const [itemsRes, membersRes, transactionsRes] = await Promise.all([
      $fetch(`${config.public.baseUrl}/api/items`),
      $fetch(`${config.public.baseUrl}/api/members`),
      $fetch(`${config.public.baseUrl}/api/transactions`)
    ])
    
    // Update stats
    stats.value.items = itemsRes?.data?.length || 0
    stats.value.members = membersRes?.data?.length || 0
    stats.value.transactions = transactionsRes?.data?.length || 0
    
    // Calculate total revenue (sum of completed transactions)
    if (transactionsRes?.data) {
      const completedTransactions = transactionsRes.data.filter(t => t.stat === 'completed')
      stats.value.revenue = completedTransactions.reduce((sum, t) => sum + (t.amount || 0), 0)
    }
    
    // Load recent items (last 5)
    if (itemsRes?.data) {
      recentItems.value = itemsRes.data
        .slice(-5)
        .map(item => ({
          id: item.id,
          name: item.name,
          price: item.price,
          quantity: item.quantity
        }))
        .reverse() // Show newest first
    }
    
    // Load recent transactions (last 5)
    if (transactionsRes?.data) {
      recentTransactions.value = transactionsRes.data
        .slice(-5)
        .map(transaction => ({
          id: transaction.id,
          type: transaction.type,
          amount: transaction.amount,
          stat: transaction.stat,
          date: transaction.date
        }))
        .reverse() // Show newest first
    }
    
  } catch (err) {
    console.error('Error loading dashboard:', err)
    snackbarColor.value = 'error'
    snackbarText.value = 'Failed to load dashboard data'
    snackbar.value = true
  } finally {
    loading.value = false
  }
}

const refreshStats = () => {
  loadDashboardData()
  snackbarColor.value = 'success'
  snackbarText.value = 'Dashboard refreshed'
  snackbar.value = true
}

const navigateTo = (path) => {
  router.push(path)
}

onMounted(() => {
  loadDashboardData()
})
</script>