<template>
  {{ listOfCurriencies}}
  <button @click="getList()" class="bg-blue-700 text-white py-2 px-4 rounded "> Get data from bank</button>
  <div
  v-for="item in data"
  >{{item.Cur_Name}} - {{ item.Cur_Abbreviation}} - {{item.Cur_Scale}}</div>
</template>

<script>
import axios from 'axios';
export default {
  name: "GetDataFromBank",
  props:['currencies'],
  data(){
    return{
      api: 'https://www.nbrb.by/api/exrates/currencies',
      data: null,
      listOfCurriencies: this.currencies,

    }
  },
  methods: {
    getList() {
      axios.get(this.api).then((response) => {
        this.data = response.data
        console.log(response.data)
      })

    }
  }
}
</script>

<style scoped>

</style>