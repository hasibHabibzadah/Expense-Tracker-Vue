<template>
  
    <Header />
    <div class="container">
      <Balance :total="+total"/>
      <IncomeExpenses :income="+income" :expenses="+expenses"/>
      <TransactionList :transactions="transactions" @transactionDeleted="handleTransactionDeleted"/>
      <AddTransaction @transactionSubmitted="handleTransactionSubmitted"/>
    </div>
</template>


<script setup>
  import Header from "./Components/Header.vue";
  import Balance from "./Components/Balance.vue";
  import IncomeExpenses from "./Components/IncomeExpenses.vue";
  import TransactionList from "./Components/TransactionList.vue";
  import AddTransaction from "./Components/AddTransaction.vue";
  import {useToast} from 'vue-toastification';
  import { computed, ref, onMounted } from "vue";

  const toast = useToast();

     const transactions = ref([
    ]);

  onMounted(() => {
    const savedTransactions = JSON.parse(localStorage.getItem("transactions"));

    if(savedTransactions){
      transactions.value = savedTransactions;
    }
  });
 
  const total = computed(() => {
    return transactions.value.reduce((acc, item) => (acc += item.amount), 0);
  });

const income = computed(() => {
    return transactions.value
    .filter((item) => item.amount > 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
  });

  const expenses = computed(() => {
    return transactions.value
    .filter((item) => item.amount < 0)
    .reduce((acc, item) => (acc += item.amount), 0)
    .toFixed(2);
  });

  // Add Transaction

  const handleTransactionSubmitted = (transactionData) => {
    transactions.value.push({
      id: generateId(),
      text: transactionData.text,
      amount: transactionData.amount,
    });

  saveToLocalStorage();
    toast.success("Transaction added successfully");  
  };

  const generateId = () => {
    return Math.floor(Math.random() * 100000000);
  };

  const handleTransactionDeleted = (id) => {
    transactions.value = transactions.value.filter((transaction) => transaction.id !== id);

    saveToLocalStorage();
    toast.success("Transaction deleted successfully");
  };
  
  // Save The LocalStorage

  const saveToLocalStorage = () => {
    localStorage.setItem("transactions", JSON.stringify(transactions.value));
  };

</script>
