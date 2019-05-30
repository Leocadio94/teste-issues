<template>
  <div class="product-stock">
    <ul>
        <li v-for="product in products">
            {{ product.quantity }} {{ product.name}}
            <span v-if="product.quantity === 0">
              - OUT OF STOCK
            </span>
        </li>
    </ul>
    <h2>Total Inventory: {{ totalProducts }} </h2>
  </div>
</template>

<script>
export default {
  name: 'ProductStock',
  data () {
    return {
        products: [],
        msg: 'Welcome to Your Vue.js App'
    }
  },
  computed: {
    totalProducts () {
      return this.products.reduce((sum, product) => {
        return sum + product.quantity
      },0)
    }
  },
  created() {
      fetch('https://api.myjson.com/bins/74l63')
        .then(response => response.json())
        .then(json => {
            this.products = json.products
        })
  }
}
</script>
