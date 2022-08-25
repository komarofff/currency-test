<template>
  {{ listOfCurriencies }}
  <p>
    <button @click="getList()" class="bg-blue-700 text-white py-2 px-4 rounded "> Get data from bank</button>
  </p>
  <div
      v-for="item in finishData">{{ item }}
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: "GetDataFromBank",
  props: ['currencies'],
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
      this.listOfCurriencies.forEach((el)=>{
        let address = this.api+'/'+ el.curCode+'?parammode=1'
        if(axios.get(address)) {
          axios.get(address).then((response) => {
            if (response.data) {
              this.finishData.push(response.data)
            }
          })
        }
      })


    }
  }
}
</script>

<style scoped>

</style>