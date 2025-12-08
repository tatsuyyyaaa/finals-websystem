<template>
  <div>
    <h1>Transactions Page</h1>
    <v-btn color="primary" @click="createTransactionDialog = true">Add Transaction</v-btn>

    <v-card class="mt-4">
      <v-data-table :items="transactions" :headers="headers" :loading="loading">
        <template v-slot:[`item.actions`]="{ item }">
          <v-btn
            icon="mdi-eye"
            color="blue"
            size="small"
            @click="viewTransaction(item)"
          ></v-btn>
          <v-btn
            icon="mdi-pencil"
            color="green"
            size="small"
            @click="editTransaction(item)"
          ></v-btn>
          <v-btn
            icon="mdi-delete"
            color="red"
            size="small"
            @click="deleteTransaction(item)"
          ></v-btn>
        </template>
      </v-data-table>
    </v-card>

    <!-- CREATE DIALOG -->
    <v-dialog width="500" v-model="createTransactionDialog">
      <v-card>
        <v-toolbar color="green">
          <v-toolbar-title>Create Transaction</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="createForm">
            <v-select v-model="transactionType" :items="types" label="Type" required></v-select>
            <v-text-field v-model="transactionAmount" label="Amount" type="number" required></v-text-field>
            <v-select v-model="transactionStat" :items="stats" label="Status" required></v-select>

            <v-btn @click="createTransaction()" color="green" block :loading="creating">Create</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- EDIT DIALOG -->
    <v-dialog width="500" v-model="editTransactionDialog">
      <v-card>
        <v-toolbar color="blue">
          <v-toolbar-title>Edit Transaction</v-toolbar-title>
        </v-toolbar>
        <v-card-text>
          <v-form class="pt-5 pb-4" ref="editForm">
            <v-select v-model="editTransactionData.type" :items="types" label="Type" required></v-select>
            <v-text-field v-model="editTransactionData.amount" label="Amount" type="number" required></v-text-field>
            <v-select v-model="editTransactionData.stat" :items="stats" label="Status" required></v-select>

            <v-btn @click="updateTransaction()" color="blue" block :loading="updating">Update</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- VIEW DIALOG -->
    <v-dialog width="500" v-model="viewTransactionDialog">
      <v-card>
        <v-toolbar color="info">
          <v-toolbar-title>Transaction Details</v-toolbar-title>
        </v-toolbar>
        <v-card-text class="pt-5">
          <v-list>
            <v-list-item>
              <v-list-item-title>Type</v-list-item-title>
              <v-list-item-subtitle>{{ viewTransactionData.type }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Amount</v-list-item-title>
              <v-list-item-subtitle>${{ viewTransactionData.amount }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Status</v-list-item-title>
              <v-list-item-subtitle>{{ viewTransactionData.stat }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Date</v-list-item-title>
              <v-list-item-subtitle>{{ viewTransactionData.date }}</v-list-item-subtitle>
            </v-list-item>
            <v-list-item>
              <v-list-item-title>Created At</v-list-item-title>
              <v-list-item-subtitle>{{ viewTransactionData.createdAt }}</v-list-item-subtitle>
            </v-list-item>
          </v-list>
        </v-card-text>
      </v-card>
    </v-dialog>

    <v-snackbar v-model="snackbar" :color="snackbarColor">
      {{ snackbarText }}
    </v-snackbar>
  </div>
</template>

<script setup>
const config = useRuntimeConfig();

const transactions = ref([]);
const loading = ref(false);
const creating = ref(false);
const updating = ref(false);

// Dialogs
const createTransactionDialog = ref(false);
const editTransactionDialog = ref(false);
const viewTransactionDialog = ref(false);

// Forms
const createForm = ref(null);
const editForm = ref(null);
const transactionType = ref("sale");
const transactionAmount = ref("");
const transactionStat = ref("pending");
const editTransactionData = ref({});
const viewTransactionData = ref({});

// Snackbar
const snackbar = ref(false);
const snackbarColor = ref("");
const snackbarText = ref("");

const types = ["sale", "purchase", "return", "refund"];
const stats = ["pending", "completed", "cancelled"];
const headers = [
  { title: "Type", key: "type" },
  { title: "Amount", key: "amount" },
  { title: "Status", key: "stat" },
  { title: "Date", key: "date" },
  { title: "Created At", key: "createdAt" },
  { title: "Actions", key: "actions", sortable: false },
];

// GET ALL TRANSACTIONS - UPDATED
const getTransactions = async () => {
  loading.value = true;
  try {
    const res = await $fetch(`${config.public.baseUrl}/api/transactions`);
    if (res && res.data) {
      // Transform Strapi response to flat objects
      transactions.value = res.data.map(transaction => ({
        id: transaction.id,
        documentId: transaction.documentId,
        type: transaction.type,
        amount: transaction.amount,
        stat: transaction.stat,
        date: transaction.date,
        createdAt: transaction.createdAt
      }));
    }
  } catch (err) {
    console.log("Error fetching transactions:", err);
    showMessage("Error loading transactions", "error");
  } finally {
    loading.value = false;
  }
};

// CREATE TRANSACTION
const createTransaction = async () => {
  if (!createForm.value) return;
  
  const { valid } = await createForm.value.validate();
  if (!valid) return;
  
  creating.value = true;
  try {
    let payload = {
      data: {
        type: transactionType.value,
        amount: transactionAmount.value,
        stat: transactionStat.value,
        date: new Date().toISOString().split('T')[0],
      },
    };
    
    const res = await $fetch(`${config.public.baseUrl}/api/transactions`, {
      method: "POST",
      body: payload,
    });

    showMessage("Transaction created successfully!", "success");
    getTransactions();
    createTransactionDialog.value = false;
    resetCreateForm();
    
  } catch (err) {
    console.log(err);
    showMessage("Error creating transaction", "error");
  } finally {
    creating.value = false;
  }
};

// VIEW TRANSACTION
const viewTransaction = (transaction) => {
  viewTransactionData.value = { ...transaction };
  viewTransactionDialog.value = true;
};

// EDIT TRANSACTION (open dialog)
const editTransaction = (transaction) => {
  editTransactionData.value = { ...transaction };
  editTransactionDialog.value = true;
};

// UPDATE TRANSACTION - UPDATED
const updateTransaction = async () => {
  if (!editForm.value) return;
  
  const { valid } = await editForm.value.validate();
  if (!valid) return;
  
  updating.value = true;
  try {
    const transactionId = editTransactionData.value.documentId || editTransactionData.value.id;
    
    let payload = {
      data: {
        type: editTransactionData.value.type,
        amount: editTransactionData.value.amount,
        stat: editTransactionData.value.stat,
      },
    };
    
    const res = await $fetch(`${config.public.baseUrl}/api/transactions/${transactionId}`, {
      method: "PUT",
      body: payload,
    });

    showMessage("Transaction updated successfully!", "success");
    getTransactions();
    editTransactionDialog.value = false;
    
  } catch (err) {
    console.log(err);
    showMessage("Error updating transaction", "error");
  } finally {
    updating.value = false;
  }
};

// DELETE TRANSACTION - UPDATED
const deleteTransaction = async (transaction) => {
  if (!confirm(`Delete this transaction?`)) return;
  
  try {
    const transactionId = transaction.documentId || transaction.id;
    
    const res = await $fetch(
      `${config.public.baseUrl}/api/transactions/${transactionId}`,
      {
        method: "DELETE",
      }
    );

    showMessage("Transaction deleted successfully!", "success");
    getTransactions();
  } catch (err) {
    console.log(err);
    showMessage("Error deleting transaction", "error");
  }
};

// Reset forms
const resetCreateForm = () => {
  transactionType.value = "sale";
  transactionAmount.value = "";
  transactionStat.value = "pending";
  if (createForm.value) {
    createForm.value.reset();
  }
};

const showMessage = (message, color = "success") => {
  snackbarText.value = message;
  snackbarColor.value = color;
  snackbar.value = true;
};

onMounted(() => {
  getTransactions();
});
</script>