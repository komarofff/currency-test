<template>
  <h1 class="text-3xl text-center font-bold">Currency rate</h1>
  <!--{{currencies}}-->
  <!--  {{currencyList }}-->
  <div class="container  mx-auto ">
    <div class="flex items-start mb-4 flex-col">
      <label class="text-xl font-bold text-left">Filter: {{ searchText }}
        <input tupe="text" v-model="searchText" @input="currencyFilter"
               class="w-[200px] text-base py-1 px-2 border border-1 border-blue-100">
      </label>
      <h2 class="text-xl font-bold text-left">Select currency:</h2>
    </div>
    <div class="flex flex-col items-start h-[30vh] overflow-hidden border border-1 border-gray-300 p-2 overflow-y-auto">
      <label v-for="val in dataFilter" :key="val.Cur_Name">
        <input type="checkbox"
               :name="labelName"
               :value="val.Cur_Name"
               :checked="val.Cur_Name == value"
               @change="getCurrencyToList(val)">
        {{ val.Cur_Name }} - {{ val.Cur_Abbreviation }} | Code -  {{ val.Cur_Code }}
      </label>
    </div>
  </div>

  <GetDataFromBank :currencies="currencies"></GetDataFromBank>


</template>

<script>
import GetDataFromBank from "@/components/GetDataFromBank";
import axios from "axios";

export default {
  name: 'App',
  data() {
    return {
      searchText: null,
      labelName: 'currency',
      selectedCurrency: null,
      currencyBankAddress: 'https://www.nbrb.by/api/exrates/currencies',
      currencyList: null,
      dataFilter: null,
      currencies: [],
    }
  },
  beforeMount() {
    if (this.currencyList == null) {
      axios.get(this.currencyBankAddress).then((response) => {
        this.currencyList = response.data
        this.dataFilter = response.data
      })
    }
  },
  computed: {
    currencyFilter() {
      if (this.searchText) {
        this.dataFilter = null
        this.dataFilter = this.currencyList.filter((el) => {
          let searchNameElement = el.Cur_Name.toLowerCase()
          //let searchCodeElement = el.Cur_Code.toLowerCase()
          let searchAbbrElement = el.Cur_Abbreviation.toLowerCase()
          let inputElement = this.searchText.toLowerCase()
          if (searchNameElement === inputElement) {
            return el
          }
          // if (searchCodeElement === inputElement) {
          //   return el
          // }
          if (searchAbbrElement === inputElement) {
            return el
          }
          if (searchNameElement.includes(inputElement)) {
            return el
          }

          // if (searchCodeElement.includes(inputElement)) {
          //   return el
          // }

          if (searchAbbrElement.includes(inputElement)) {
            return el
          }
        })

      } else {
        this.dataFilter = this.currencyList
      }
    }
  },
  methods: {
    getCurrencyToList(val) {
      if(!this.currencies.find(el => el.curCode === val.Cur_Code)) {
        this.currencies.push({
          curCode: val.Cur_Code,
          curName: val.Cur_Name,
          curAbbr: val.Cur_Abbreviation,
          curScale: val.Cur_Scale
        })
      }else{
       let indexOfData =  this.currencies.findIndex(el => el.curCode === val.Cur_Code)
        this.currencies.splice(indexOfData,1)
      }
    }
  },
  components: {
    GetDataFromBank
  }
}
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
