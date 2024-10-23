<template>
  <Header/>
  <div class="container">
    <Balance :total="+total"/>
    <Income :income="+income" :expenses="+expenses"/>
    <Transactions :transactions="transactions" @transactionDeleted="handleDeleteTransaction"/>
    <AddTransaction @transactionSubmitted="handleTransSubmitted"/>
  </div>
</template>

<script setup>
  import Header from "./components/Header.vue";
  import Balance from "./components/Balance.vue";
  import Income from "./components/Income.vue";
  import Transactions from "./components/Transactions.vue";
  import AddTransaction from "./components/AddTransaction.vue";
  import dummyData from "./data/dummy.json";
  import {ref, computed, onMounted} from "vue";
  import {useToast} from "vue-toastification";

  const transactions=ref(dummyData);
  const toast=useToast();

  onMounted(()=> {
    const savedTransactions=JSON.parse(localStorage.getItem("transactions"));

    if(savedTransactions){
      transactions.value=savedTransactions;
    }
  });

  const generateUniqueId=()=> {
    return transactions.value.length+1;
  };

  const handleTransSubmitted=(transactionData)=> {
    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    });

    saveTransactionsToLocalStorage();

    toast.success("Transaction added successfully");
  };

  const handleDeleteTransaction=(id)=> {
    transactions.value=transactions.value.filter(transaction=> transaction.id!==id);

    saveTransactionsToLocalStorage();

    toast.success("Transaction deleted successfully");
  };

  const total=computed(()=> {
    return transactions.value.reduce((acc, transaction)=> {
      return acc+transaction.amount
    }, 0);
  });

  const income=computed(()=> {
    return transactions.value.filter(transaction=> transaction.amount>0).reduce((acc, transaction)=> {
      return acc+transaction.amount
    }, 0).toFixed(2);
  });

  const expenses=computed(()=> {
    return transactions.value.filter(transaction=> transaction.amount<0).reduce((acc, transaction)=> {
      return acc+transaction.amount
    }, 0).toFixed(2);
  });

  const saveTransactionsToLocalStorage=()=> {
    localStorage.setItem("transactions", JSON.stringify(transactions.value));
  }
</script>