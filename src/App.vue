<template :key="appNumber">
  <div class="px-4 mt-3">
    <h1 class="text-3xl text-center font-bold">Currency rate</h1>
    <div class="container  mx-auto ">
      <div class="mt-3">
        <label class="text-xl font-bold text-left ">Select date
          <input type="date" v-model="date" :max="maxDate"
                 class="w-[200px] text-base py-1 px-2 border border-1 border-blue-100">
        </label>
      </div>
      <div class="grid grid-cols-1 lg:grid-cols-2 gap-4 items-end lg:mt-0 mt-4">
        <section>
          <div class="flex items-start mb-2 flex-col">
            <div class="flex items-center justify-start flex-wrap">
              <h2 class="text-xl font-bold text-left mr-6 mb-0">Select currency:</h2>
              <label class="text-xl font-bold text-left flex items-center justify-start">Filter
                <input type="text" v-model="searchText"
                       class="w-[200px] text-base py-1 px-2 border border-1 border-blue-100 ml-2">
              </label>
            </div>
          </div>
          <div
              class="flex flex-col items-start h-[40vh] lg:h-[60vh] overflow-hidden border border-1 border-gray-300 p-2 overflow-y-auto text-left">
            <template v-for="val in dataFilter" :key="val.Cur_ID">
              <label>
                <input type="checkbox"
                       :name="val.Cur_Abbreviation"
                       :id="'ID'+val.Cur_ID"
                       :value="val.Cur_Name"
                       @change="getCurrencyToList(val)"
                       :checked="false"
                >
                {{ val.Cur_Name }} - {{ val.Cur_Abbreviation }} - {{ val.Cur_Code }}

              </label>
            </template>
          </div>
        </section>
        <section>
          <div class="flex items-start mb-2 flex-col">
            <h2 class="text-xl font-bold text-left">Selected currency:</h2>
          </div>
          <div
              class="flex flex-col items-start h-[40vh] lg:h-[60vh] overflow-hidden border border-1 border-gray-300 p-2 overflow-y-auto">
            <template v-for="val in currencies" :key="val.curId">
              <p class="flex items-start text-left py-1 border-1 border-b border-gray-100 w-full">
                <svg @click="delCurrencyToList(val)" xmlns="http://www.w3.org/2000/svg"
                     class="stroke-red-700 mb-1 cursor-pointer mr-3 min-w-[24px]" width="24"
                     height="24" viewBox="0 0 24 24" stroke-width="2" stroke="currentColor" fill="none"
                     stroke-linecap="round" stroke-linejoin="round">
                  <path stroke="none" d="M0 0h24v24H0z" fill="none"></path>
                  <path d="M4 7h16"></path>
                  <path d="M5 7l1 12a2 2 0 0 0 2 2h8a2 2 0 0 0 2 -2l1 -12"></path>
                  <path d="M9 7v-3a1 1 0 0 1 1 -1h4a1 1 0 0 1 1 1v3"></path>
                  <path d="M10 12l4 4m0 -4l-4 4"></path>
                </svg>
                <span> {{ val.curName }}
                <template v-if="val.finishRate">
               |  за {{ val.finishScale }}  {{ val.finishName }}  -  {{ val.finishRate }} BLR
                </template>

              </span>
              </p>
            </template>
          </div>
        </section>
      </div>
    </div>
    <GetDataFromBank :currencies="currencies" :date="date" @getData="PutFinishData" :key="appKey"></GetDataFromBank>
  </div>
</template>

<script>
import GetDataFromBank from "@/components/GetDataFromBank";
import axios from "axios";

export default {
  name: 'App',
  data() {
    return {
      appKey: 0,
      date: new Date().getFullYear() + '-' + ('0' + (new Date().getMonth() + 1)).slice(-2) + '-' + ('0' + (new Date().getDate())).slice(-2),
      maxDate: new Date().getFullYear() + '-' + ('0' + (new Date().getMonth() + 1)).slice(-2) + '-' + ('0' + (new Date().getDate())).slice(-2),
      appNumber: 0,
      searchText: null,
      selectedCurrency: null,
      currencyBankAddress: 'https://www.nbrb.by/api/exrates/currencies',
      currencyList: null,
      dataFilter: [],
      currencies: [],
      dataFromServer: []
    }
  },
  beforeMount() {
    if (this.currencyList == null) {
      axios.get(this.currencyBankAddress).then((response) => {
        let arr = response.data
        this.currencyList = response.data
        this.dataFilter = response.data
      })
    }
  },
  watch: {
    searchText(val) {
      let data = null
      this.dataFilter = []
      if (val.length > 0) {
        data = this.currencyList.filter(el => {
          if (el.Cur_Abbreviation.toLowerCase().includes(val.toLowerCase())) {
            return el
          }
          if (el.Cur_Name.toLowerCase().includes(val.toLowerCase())) {
            return el
          }
          if (el.Cur_Code.toLowerCase().includes(val.toLowerCase())) {
            return el
          }
        })
      } else {
        data = this.currencyList
      }
      this.dataFilter = data
    },
    date() {
      this.appKey++
      this.appNumber++
    }
  },
  computed: {},
  methods: {

    getCurrencyToList(val) {
      if (!this.currencies.find(el => el.curId === val.Cur_ID)) {
        this.currencies.push({
          curId: val.Cur_ID,
          curCode: val.Cur_Code,
          curName: val.Cur_Name,
          curAbbr: val.Cur_Abbreviation,
          curScale: val.Cur_Scale,
          finishName: null,
          finishScale: null,
          finishRate: null
        })
      } else {
        let indexOfData = this.currencies.findIndex(el => el.curId === val.Cur_ID)
        this.currencies.splice(indexOfData, 1)
      }
    },
    delCurrencyToList(val) {
      if (this.currencies.find(el => el.curId === val.curId)) {
        let indexOfData = this.currencies.findIndex(el => el.curId === val.curId)
        this.currencies.splice(indexOfData, 1)
        let id = `ID${val.curId}`
        if (document.getElementById(id)) document.getElementById(id).checked = false
      }
      if (this.dataFromServer.find(el => el.Cur_Abbreviation === val.curAbbr)) {
        let indexOfData = this.dataFromServer.findIndex(el => el.Cur_Abbreviation === val.curAbbr)
        this.dataFromServer.splice(indexOfData, 1)

      }
    },
    PutFinishData(val) {
      if (!this.dataFromServer.find(elem => elem.Cur_ID === val.Cur_ID)) {
        this.dataFromServer.push(val)
      }
      let indexOfData = this.currencies.findIndex(el => el.curAbbr === val.Cur_Abbreviation)
      if (indexOfData !== null && indexOfData !== -1) {
        this.currencies[indexOfData].finishRate = val.Cur_OfficialRate
        this.currencies[indexOfData].finishName = val.Cur_Name
        this.currencies[indexOfData].finishScale = val.Cur_Scale
      }
      this.appNumber++
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
}
</style>
