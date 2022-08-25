<template>
<!-- {{ listOfCurriencies }}-->
  <p class="my-4">
    <button @click="getList()" class="bg-blue-700 text-white py-2 px-4 rounded "> Get data from bank</button>
  </p>
  response.data={{finishData}}
<!--  <div v-for="item in finishData">{{ item }}-->
<!--  </div>-->
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
      data: null,
      listOfCurriencies: this.currencies,
      finishData: []
    }
  },
  methods: {
    getList() {
      this.finishData = []
      this.listOfCurriencies.forEach((el) => {
        let address = this.api + '/' + el.curCode + '?parammode=1'
        if (axios.get(address)) {
          axios.get(address).then((response) => {
            if (response.data) {
              this.finishData.push(response.data)
            }
          })
        }
      })
      if(this.finishData){this.$emit('getData',this.finishData)}


    }
  }
}
</script>

<style scoped>

</style>