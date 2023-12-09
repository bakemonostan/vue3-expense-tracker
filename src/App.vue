<template>
  <Header />
  <div class="container">
    <Balance :total="total" />
    <IncomeExpense :income="income" :expenses="expenses" />
    <TransactionList
      :transactions="transactions"
      @transactionDeleted="handleDelete"
    />
    <AddTransaction @addTransaction="handleSubmit" />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from "vue";
import Header from "./components/Header.vue";
import Balance from "./components/Balance.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";

import { useToast } from "vue-toastification";

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const storedTransactions = JSON.parse(localStorage.getItem("transactions"));
  if (storedTransactions) {
    transactions.value = storedTransactions;
  }
});

// Calculate total
const total = computed(() => {
  return transactions.value.reduce((acc, transaction) => {
    return acc + transaction.amount;
  }, 0);
});

// Get income
const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

// Get expenses
const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

const handleSubmit = (transaction) => {
  transactions.value.push({
    id: Math.floor(Math.random() * 1000000000),
    ...transaction,
  });
  toast.success("Transaction added");
  saveTransactionsToStorage();
};

const handleDelete = (id) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== id
  );
  saveTransactionsToStorage();
  toast.error("Transaction deleted");
};

// save to local storage
const saveTransactionsToStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};
</script>
