<template>
  <div id="app">
    <TokenConverter />
  </div>
</template>

<script>
import TokenConverter from './components/TokenConverter.vue'

export default {
  name: 'App',
  components: {
    TokenConverter
  }
}
</script>

<style>
body {
  background-color: #EDF2F7;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}

/* Chrome, Safari, Edge, Opera */
input::-webkit-outer-spin-button,
input::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}

/* Firefox */
input[type=number] {
  -moz-appearance: textfield;
}
</style>
