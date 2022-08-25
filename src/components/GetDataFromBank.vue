<template>
  <p class="my-4">
    <button @click="getList()" class="bg-blue-700 text-white py-2 px-4 rounded "> Get data from bank</button>
  </p>
</template>

<script>
import axios from 'axios';

export default {
  name: "GetDataFromBank",
  props: ['currencies'],
  emits: ['getData'],
  data() {
    return {
      api: 'https://www.nbrb.by/api/exrates/rates',
      listOfCurriencies: this.currencies,
      finishData: []
    }
  },
  methods: {
    getList() {
      this.finishData = []
      this.listOfCurriencies.forEach((el) => {
        let address = this.api + '/' + el.curCode + '?parammode=1'
          axios.get(address).then((response) => {
            if (response.data) {
              this.finishData.push(response.data)
              this.$emit('getData',response.data)
            }

          }).catch((error) => {
            if(error.response && error.response.status === 404) {
              console.clear();
            }
          })

      })

    }
  }
}
</script>

<style scoped>

</style>