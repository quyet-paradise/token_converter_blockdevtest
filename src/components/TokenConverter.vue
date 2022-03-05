<template>
  <div id="main" class="w-full max-w-xs">
    <form class="bg-white shadow-md rounded px-6 pt-6 pb-8 mb-4">
      <div class="mb-8">
        <span class="font-semibold text-2xl tracking-tight">Token Converter</span>
      </div>

      <div class="mb-4">
        <div class="flex items-center border-x border-y border-gray-400 py-2 px-3 rounded">
          <input v-model="firstTokenAmount" @input="onFirstInput" class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none" type="number" placeholder="0.0" aria-label="First Token Amount">
          <div class="inline-block relative w-64">
            <select v-model="firstTokenSymbol" @change="onChangeFirstSelectToken" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-2 py-2 rounded shadow leading-tight focus:outline-none focus:shadow-outline">
              <option v-for="(item, idx) in tokensList" :key="idx">{{ item.symbol }}</option>
            </select>
            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
              <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
            </div>
          </div>
        </div>
      </div>

      <div class="mb-4">
        <button @click="onSwapBtn" type="button" data-bs-toggle="tooltip" data-bs-placement="right" title="Swap" class="bg-transparent hover:bg-blue-500 text-gray-800 hover:text-white font-bold py-2 px-2 rounded-full items-center">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-arrow-down-up" viewBox="0 0 16 16">
            <path fill-rule="evenodd" d="M11.5 15a.5.5 0 0 0 .5-.5V2.707l3.146 3.147a.5.5 0 0 0 .708-.708l-4-4a.5.5 0 0 0-.708 0l-4 4a.5.5 0 1 0 .708.708L11 2.707V14.5a.5.5 0 0 0 .5.5zm-7-14a.5.5 0 0 1 .5.5v11.793l3.146-3.147a.5.5 0 0 1 .708.708l-4 4a.5.5 0 0 1-.708 0l-4-4a.5.5 0 0 1 .708-.708L4 13.293V1.5a.5.5 0 0 1 .5-.5z"/>
          </svg>
        </button>
      </div>

      <div class="mb-4">
        <div class="flex items-center border-x border-y border-gray-400 py-2 px-3 rounded">
          <input v-model="secondTokenAmount" @input="onSecondInput" class="appearance-none bg-transparent border-none w-full text-gray-700 mr-3 py-1 px-2 leading-tight focus:outline-none" type="number" placeholder="0.0" aria-label="Second Token Amount">
          <div class="inline-block relative w-64">
            <select v-model="secondTokenSymbol" @change="onChangeSecondSelectToken" class="block appearance-none w-full bg-white border border-gray-400 hover:border-gray-500 px-2 py-2 rounded shadow leading-tight focus:outline-none focus:shadow-outline">
              <option v-for="(item, idx) in tokensList" :key="idx">{{ item.symbol }}</option>
            </select>
            <div class="pointer-events-none absolute inset-y-0 right-0 flex items-center px-2 text-gray-700">
              <svg class="fill-current h-4 w-4" xmlns="http://www.w3.org/2000/svg" viewBox="0 0 20 20"><path d="M9.293 12.95l.707.707L15.657 8l-1.414-1.414L10 10.828 5.757 6.586 4.343 8z"/></svg>
            </div>
          </div>
        </div>
      </div>

      <div class="mb-4">
        <div class="inline-block relative text-xs">
          <span class="font-bold text-blue-500">Price:&nbsp;</span>
          <span>{{ exchangeRate }}&nbsp;</span>
          <span>{{ secondTokenSymbol }}&nbsp;</span>
          <span>per&nbsp;</span>
          <span>{{ firstTokenSymbol }}</span>
          <button @click="onRefreshPriceBtn" type="button" data-bs-toggle="tooltip" data-bs-placement="right" title="Refresh Price" class="bg-blue-500 hover:bg-black text-white hover:text-white font-bold mx-2 py-1 px-1 rounded-full items-center">
            <svg xmlns="http://www.w3.org/2000/svg" width="13" height="13" fill="currentColor" class="bi bi-arrow-repeat" viewBox="0 0 16 16">
              <path d="M11.534 7h3.932a.25.25 0 0 1 .192.41l-1.966 2.36a.25.25 0 0 1-.384 0l-1.966-2.36a.25.25 0 0 1 .192-.41zm-11 2h3.932a.25.25 0 0 0 .192-.41L2.692 6.23a.25.25 0 0 0-.384 0L.342 8.59A.25.25 0 0 0 .534 9z"/>
              <path fill-rule="evenodd" d="M8 3c-1.552 0-2.94.707-3.857 1.818a.5.5 0 1 1-.771-.636A6.002 6.002 0 0 1 13.917 7H12.9A5.002 5.002 0 0 0 8 3zM3.1 9a5.002 5.002 0 0 0 8.757 2.182.5.5 0 1 1 .771.636A6.002 6.002 0 0 1 2.083 9H3.1z"/>
            </svg>
          </button>
        </div>
      </div>
    </form>
  </div>
</template>

<script>
export default {
  data() {
    return {
      firstToken: {},
      secondToken: {},
      firstTokenAmount: null,
      secondTokenAmount: null,
      tokensData: {},
      tokensList: []
    }
  },
  created() {
    fetch('https://api.pancakeswap.info/api/v2/tokens')
      .then(res => res.json())
      .then(data => {
        this.tokensData = data.data;
        for(let i in data.data) {
          this.tokensList.push(data.data[i]);
        }
        this.firstToken = this.tokensList[Math.floor(Math.random() * this.tokensList.length)];
        this.secondToken = this.tokensList[Math.floor(Math.random() * this.tokensList.length)];
      })
      .catch(err => console.log(err.message));
  },
  mounted() {},
  methods: {
    onChangeFirstSelectToken(event) {
      const tokenFound = this.tokensList.find(element => element.symbol === event.target.value);
      this.firstToken = tokenFound;
    },
    onChangeSecondSelectToken(event) {
      const tokenFound = this.tokensList.find(element => element.symbol === event.target.value);
      this.secondToken = tokenFound;
    },
    onFirstInput(event) {
      this.firstTokenAmount = parseFloat(event.target.value);
      let exchangeRate = this.firstToken.price / this.secondToken.price;
      this.secondTokenAmount = (this.firstTokenAmount * exchangeRate).toFixed(4);
    },
    onSecondInput(event) {
      this.secondTokenAmount = parseFloat(event.target.value);
      let exchangeRate = this.secondToken.price / this.firstToken.price;
      this.firstTokenAmount = (this.secondTokenAmount * exchangeRate).toFixed(4);
    },
    onSwapBtn() {
      let tempToken = this.firstToken;
      let tempAmount = this.firstTokenAmount;
      this.firstToken = this.secondToken;
      this.secondToken = tempToken;
      this.firstTokenAmount = this.secondTokenAmount;
      this.secondTokenAmount = tempAmount;
    },
    onRefreshPriceBtn() {
      fetch('https://api.pancakeswap.info/api/v2/tokens')
      .then(res => res.json())
      .then(data => {
        this.tokensData = data.data;
        for(let i in data.data) {
          this.tokensList.push(data.data[i]);
        }
        let newFirstToken = this.tokensList.find(element => element.symbol === this.firstToken.symbol);
        let newSecondToken = this.tokensList.find(element => element.symbol === this.secondToken.symbol);
        this.firstToken = newFirstToken;
        this.secondToken = newSecondToken;
        if(this.firstTokenAmount !== null) {
          let exchangeRate = this.firstToken.price / this.secondToken.price;
          this.secondTokenAmount = (this.firstTokenAmount * exchangeRate).toFixed(4);
        }
      })
      .catch(err => console.log(err.message));
    }
  },
  computed: {
    exchangeRate() {
      if(this.firstToken !== {} && this.secondToken !== {}) {
        return (this.firstToken.price / this.secondToken.price).toFixed(4);
      } else {
        return null
      }
    },
    firstTokenSymbol: {
      get() {
        return this.firstToken !== {} ? this.firstToken.symbol : '' ;
      }, 
      set(newValue) {
        return newValue
      }
    },
    secondTokenSymbol: {
      get() {
        return this.secondToken !== {} ? this.secondToken.symbol : '' ;
      }, 
      set(newValue) {
        return newValue
      }
    }
  }
}
</script>

<style>
#main {
  margin: 5rem auto;
}
</style>