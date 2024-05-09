<template>
  <div>
    <Header />
    <div class="container">
      <Balance :total="total" />
      <income-expense :income="income" :expenses="expenses" />
      <add-transaction @transaction-submitted="handleTransaction" />
      <transaction-list
        :transactions="transactions"
        @transaction-deleted="deleteTransaction"
      />
    </div>
  </div>
</template>

<script setup>
import Balance from "./components/Balance.vue";
import Header from "./components/Header.vue";
import IncomeExpense from "./components/IncomeExpense.vue";
import TransactionList from "./components/TransactionList.vue";
import AddTransaction from "./components/AddTransaction.vue";
import { useToast } from "vue-toastification";
import { computed, onMounted, ref } from "vue";

const transactions = ref([]);

onMounted(() => {
  const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

  if (savedTransactions) {
    transactions.value = savedTransactions;
  }
});

const toast = useToast();

const total = computed(() => {
  return transactions.value
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

const income = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount > 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

const expenses = computed(() => {
  return transactions.value
    .filter((transaction) => transaction.amount < 0)
    .reduce((acc, transaction) => {
      return acc + transaction.amount;
    }, 0)
    .toFixed(2);
});

const handleTransaction = (transactionData) => {
  transactions.value.push({
    id: generateUniqueId(),
    text: transactionData.text,
    amount: transactionData.amount,
  });

  saveTransactionToLocalStorage();

  toast.success("Transaction added!");
};

const deleteTransaction = (transactionId) => {
  transactions.value = transactions.value.filter(
    (transaction) => transaction.id !== transactionId
  );
  saveTransactionToLocalStorage();
};

const saveTransactionToLocalStorage = () => {
  localStorage.setItem("transactions", JSON.stringify(transactions.value));
};

const generateUniqueId = () => {
  return Math.floor(Math.random() * 1000000);
};
</script>
