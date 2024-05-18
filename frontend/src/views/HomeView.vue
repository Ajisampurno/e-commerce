<template>
  <div class="container mx-auto">
    <!-- Header -->
    <header class="py-4 border-b bg-white mx-auto max-w-7xl fixed-nav">
      <div class="flex justify-between items-center">
        <h1 class="text-3xl font-semibold text-gray-800">Hijja</h1>
        <div class="flex items-center space-x-4">
          <input
            v-model="searchQuery"
            @input="searchProducts"
            type="text"
            placeholder="Search products"
            class="border rounded-lg px-3 py-2"
          />
        </div>
        <nav>
          <ul class="flex space-x-4">
            <li><a href="#" class="text-blue-500 hover:text-blue-700">Home</a></li>
            <li><a href="#" class="text-blue-500 hover:text-blue-700">Products</a></li>
            <li><a href="#" class="text-blue-500 hover:text-blue-700">About</a></li>
            <li><a href="#" class="text-blue-500 hover:text-blue-700">Contact</a></li>
            <!-- Shopping Cart -->
            <li>
              <button @click="showCart = !showCart" class="relative">
                <span class="material-icons">shopping_cart</span>
                <!-- Show Cart Items Count -->
                <span
                  v-if="cart.length > 0"
                  class="absolute bottom-4 left-5 bg-red-500 text-white rounded-full px-2 py-1 text-xs"
                  >{{ cart.length }}</span
                >
              </button>
            </li>
          </ul>
        </nav>
      </div>
    </header>

    <main class="py-8">
      <!--Carousel-->
      <section class="max-w-6xl mx-auto mt-11">
        <Carousel />
      </section>

      <!--Product-->
      <section class="max-w-6xl mx-auto mt-11">
        <h2 class="text-2xl font-semibold mb-4">Products</h2>
        <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-4 gap-4">
          <div
            v-for="product in filteredProducts"
            :key="product.id"
            class="bg-white rounded-lg shadow-md overflow-hidden"
          >
            <img
              :src="product.image"
              :alt="product.name"
              class="w-full h-70 object-cover object-center"
            />
            <div class="p-4">
              <h3 class="text-xl font-semibold mb-2">{{ product.name }}</h3>
              <p class="text-gray-600">{{ product.price }}</p>
              <button
                @click="addToCart(product)"
                class="mt-2 bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded"
              >
                Add to Cart
              </button>
            </div>
          </div>
        </div>
      </section>

      <section class="py-4 border-b bg-white mx-auto max-w-7xl">
        <Footer />
      </section>
    </main>
  </div>

  <!-- Shopping Cart Dropdown -->
  <div
    v-if="showCart"
    class="absolute top-20 right-0 bg-white border border-gray-200 mt-2 rounded-lg shadow-lg z-10"
  >
    <div v-if="cart.length > 0" class="p-4">
      <p class="text-lg font-semibold mb-2">Shopping Cart</p>
      <ul>
        <li v-for="(item, index) in cart" :key="item.id" class="flex justify-between mb-2">
          <span>{{ item.name }}</span>
          <span>{{ item.price }}</span>
          <button @click="removeFromCart(index)" class="text-red-500">Remove</button>
        </li>
      </ul>
      <button
        @click="showCart = false"
        class="block w-full bg-blue-500 text-white py-2 px-4 rounded-lg"
      >
        Close
      </button>
    </div>
    <div v-else class="p-4">
      <p>Your cart is empty</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios'
import Carousel from '@/components/Carousel.vue'
import Footer from '@/components/Footer.vue'

export default {
  data() {
    return {
      products: [],
      filteredProducts: [],
      cart: [],
      showCart: false,
      searchQuery: ''
    }
  },
  components: {
    Carousel,
    Footer
  },
  methods: {
    async fetchData() {
      try {
        const response = await axios.get('https://sistemtoko.com/public/demo/product')
        this.products = response.data.aaData.map((product) => ({
          id: product.id,
          name: product.name,
          price: product.price,
          image: product.photo
        }))
        this.filteredProducts = this.products
      } catch (error) {
        console.error('Error fetching products:', error)
      }
    },
    searchProducts() {
      this.filteredProducts = this.products.filter((product) =>
        product.name.toLowerCase().includes(this.searchQuery.toLowerCase())
      )
    },
    addToCart(product) {
      this.cart.push(product)
    },
    removeFromCart(index) {
      this.cart.splice(index, 1)
    }
  },
  mounted() {
    this.fetchData()
  }
}
</script>

<style scoped>
.carousel-item {
  display: flex;
  justify-content: center;
  align-items: center;
}
.fixed-nav {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 999;
}
</style>
