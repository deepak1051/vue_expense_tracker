<template>
  <Header />
  <div class="container">
    <Balance :total="+total" />
    <IncomeExpenses :income="+income" :expenses="+expenses" />
    <TransactionList
      :transactions="transactions"
      @deleteTransaction="deleteTransaction"
    />
    <AddTransaction @transactionSubmitted="handleTransaction" />
  </div>
</template>

<script setup>
import Header from './components/Header.vue';
import Balance from './components/Balance.vue';
import IncomeExpenses from './components/IncomeExpenses.vue';
import TransactionList from './components/TransactionList.vue';
import AddTransaction from './components/AddTransaction.vue';

import { ref, computed, onMounted } from 'vue';

import { useToast } from 'vue-toastification';

const toast = useToast();

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem('transactions'));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

//get total amount

const total = computed(() => {
  return transactions.value
    .reduce((acc, curr) => acc + curr.amount, 0)
    .toFixed(2);
});

//get income
const income = computed(() => {
  return transactions.value
    .filter((t) => t.amount > 0)
    .reduce((acc, curr) => acc + curr.amount, 0)
    .toFixed(2);
});

//get expenses

const expenses = computed(() => {
  return transactions.value
    .filter((t) => t.amount < 0)
    .reduce((acc, curr) => acc + curr.amount, 0)
    .toFixed(2);
});

// handle transaction
const handleTransaction = (transactionData) => {
  transactions.value.push({
    id: generateID(),
    text: transactionData.text,
    amount: +transactionData.amount,
  });

  saveTransactionsToLocalStorage();

  toast.success('Transaction Added');
};

const generateID = () => {
  return Math.floor(Math.random() * 100000000);
};

const deleteTransaction = (transaction) => {
  transactions.value = transactions.value.filter(
    (t) => t.id !== transaction.id
  );

  saveTransactionsToLocalStorage();
};

//save to localstorage
const saveTransactionsToLocalStorage = () => {
  localStorage.setItem('transactions', JSON.stringify(transactions.value));
};
</script>
