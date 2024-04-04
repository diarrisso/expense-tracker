<template>
    <Header/>
    <div class="container">
      <Balance :total="+total"/>
      <IncomeExpense :income="+income" :expense="+expense"/>
      <TransactionList :transactions="transactions" @deleteTransactionId="handleDeletTransaction"/>
      <AddTransaction @transactioSubmitted="handleTransactionSubmitted"/>

    </div>

</template>


<script setup>

  import Header from '@/components/Header.vue';
  import Balance from '@/components/Balance.vue';
  import IncomeExpense from '@/components/IncomeExpense.vue';
  import TransactionList from '@/components/TransactionList.vue';
  import AddTransaction from '@/components/AddTransaction.vue';
  import {useToast} from 'vue-toastification';

  import { ref, computed, onMounted} from 'vue';

  const toast = useToast();

  const  transactions = ref([]);

  onMounted(() => {
    const saveTransactions = JSON.parse(localStorage.getItem (transactions));
    if(saveTransactions) {
      transactions.value = saveTransactions
    }

  })

  // Get total
  const total = computed(() => {
    return transactions.value.reduce((acc, transaction) => {
      return acc + transaction.amount;

    }, 0);
  });

  // Get income
  const income = computed(() => {
    return transactions.value.filter((transaction)=> transaction.amount > 0)
      .reduce((acc, transaction) => {
      return acc + transaction.amount;

    }, 0)
    // .toFixed(2);
  });

  // Get expense
  const expense = computed(() => {
    return transactions.value
      .filter((transaction)=> transaction.amount < 0)
      .reduce((acc, transaction) => {
        return acc + transaction.amount;

      }, 0);
    //  .toFixed(2);
  });

  // add transaction
  const handleTransactionSubmitted = (transactionData) => {

    transactions.value.push({
      id: generateUniqueId(),
      text: transactionData.text,
      amount: transactionData.amount
    });



    toast.success('transaction ist added');
  };

  // get unique Id
  const generateUniqueId = () => {
    return Math.floor(Math.random() * 1000000);
  }

  // detele transaction 
  const handleDeletTransaction = (id) => {
    transactions.value = transactions.value.filter(
      (transaction) => transaction.id !== id 
    );


    saveTransactionsToLocalStorage();

    toast.success('transaction is deleted');
  }


  // Save localStorage

  const saveTransactionsToLocalStorage = () => {
   localStorage.setItem('transactions', JSON.stringify(transactions, value));


  }


</script>