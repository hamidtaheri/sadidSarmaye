<template>
  <b-row>
    <b-col cols="12">
      <table-kitchen-sink :items="items" title="لیست کاربران"/>
      <table-user-transactions :items="userTransactions" title="لیست تراکنش ها"/>
    </b-col>
  </b-row>
</template>

<script>
import {BRow, BCol} from 'bootstrap-vue'
import TableKitchenSink from './TableKitchenSink.vue'
import TableUserTransactions from './TableUserTransactions.vue'
import {PROFILE_FOR_DROPDOWN,USER_TRANSACTIONS} from "@/constants/graphql";

export default {
  data() {
    return {
      items: [
        {
          firstName: 'سیدروح الله',
          lastName: 'سیدموسوی',
          codeMeli: '037027',
          mobile: '09127510',
          moaref: 'سیدروح الله علم الهدی',
          totalSum: '153458000',
          status: Math.floor(Math.random() * 4) + 1
        }
      ],
      userTransactions: [
        {
          id: '',
          kindId: '',
          amount: '',
          date: ''
        }
      ]
    }
  },
  created() {
    var profiles = new Array(), items
    this.$apollo.mutate({
      mutation: PROFILE_FOR_DROPDOWN,
    }).then((result) => {
      items = result.data.profiles.edges
      for (const item of items) {
        profiles.push({
          "id": item.node.id,
          "firstName": item.node.firstName,
          "lastName": item.node.lastName,
          "codeMeli": item.node.codeMeli,
          "mobile": item.node.mobile,
          "presenter": item.node.presenter,
          "status": Math.floor(Math.random() * 4) + 1
        })
      }
      this.items = profiles
    }).catch((error) => {
      this.logout();
    })

    this.$apollo.mutate({
      mutation: USER_TRANSACTIONS,
      variables:{
        profile_Id:3
      }
    }).then((result) => {
      var transactions=new Array()
      items = result.data.transactions.edges
      for (const item of items) {
        transactions.push({
          "id": item.node.id,
          "kindId": item.node.kind.id,
          "amount": item.node.amount,
          "date": item.node.effectiveDate,
        })
      }
      this.userTransactions = transactions
    }).catch((error) => {
      console.log(error)
    })

  },
  components: {
    BRow,
    BCol,
    TableKitchenSink,
    TableUserTransactions
  },
}
</script>

<style lang="scss">
@import '@core/scss/vue/libs/vue-select.scss';
</style>
