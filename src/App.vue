<template>
  <div id="app">
    <ProductStock/>
  </div>
</template>

<script>
import ProductStock from './components/ProductStock'

export default {
  name: 'App',
  components: {
    ProductStock
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
