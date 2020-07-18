<template>
  <div>
    <my-header :cartItemCount="cartItemCount"></my-header>
    <main>
      <div
        class="row product align-items-center"
        v-for="product in sortedProducts"
        v-bind:key="product.id"
      >
        <div class="col-md-5 offset-md-0">
          <figure>
            <img class="product" v-bind:src="product.image" />
          </figure>
        </div>
        <div class="col-md-6 offset-md-0 description">
          <router-link
            tag="h1"
            :to="{ name: 'Id', params: {id: product.id} }"
          >
            {{ product.title }}
          </router-link>
          <p v-html="product.description"></p>
          <p>{{ product.price | formatPrice }}</p>
          <button
            class="btn btn-primary btn-lg"
            v-on:click="addToCart(product)"
            v-if="canAddToCart(product)"
          >
            Add to cart
          </button>
          <button class="btn btn-primary btn-lg" disabled v-else>Add to cart</button>
          <transition name="bounce" mode="out-in">
            <span
              class="inventory-message"
              v-if="product.availableInventory - cartCount(product.id) === 0"
              key="0"
            >
              All out!
            </span>
            <span
              class="inventory-message"
              v-else-if="product.availableInventory - cartCount(product.id) < 5"
              key=""
            >
              Only {{product.availableInventory - cartCount(product.id)}} left!
            </span>
            <span class="inventory-message" v-else key="">Buy now!</span> 
          </transition>
          <div class="rating">
            <span
              v-bind:class="{'rating-active' : checkRating(n, product)}"
              v-bind:key="n"
              v-for="n in 5"
            >
              ☆
            </span>
          </div>
        </div>
        <hr />
      </div>
    </main>
  </div>
</template>

<script>
import MyHeader from './Header.vue';
export default {
  name: 'imain',
  data() {
    return {
      products: {},
      cart: []
    };
  },  
  computed: {
    cartItemCount: function() {
      return this.cart.length || '';
    },
    sortedProducts: function() {
      if (this.products.length > 0) {
        const productsArray = this.products.slice(0);
        function compare(a, b) {
          if (a.title.toLowerCase() < b.title.toLowerCase())
            return -1;
          if (a.title.toLowerCase() > b.title.toLowerCase())
            return 1;
          return 0;
        }
        return productsArray.sort(compare);
      }
    }
  },
  components: { MyHeader },
  methods: {
    addToCart: function(aProduct) {
      this.cart.push(aProduct.id);
    },        
    canAddToCart(aProduct) {
      return aProduct.availableInventory > this.cartCount(aProduct.id);
    },
    cartCount(id) {
      let count = 0;
      for (let i = 0; i < this.cart.length; i++) {
        if (this.cart[i] === id) {
          count++;
        }
      }
      return count;
    },
    checkRating(n, myProduct) {
      return myProduct.rating - n >= 0;
    }
  },
  filters: {
    formatPrice: function(price) {
      if (!parseInt(price)) { return ''; }
      if (price > 99999) {
        const priceString = (price / 100).toFixed(2);
        const priceArray = priceString.split('').reverse();
        let index = 3;
        while (priceArray.length > index + 3) {
          priceArray.splice(index + 3, 0, ',');
          index += 4;
        }
        return '$' + priceArray.reverse().join('');
      } else {
        return '$' + (price / 100).toFixed(2);
      }
    }
  },
  created: function() {
    axios.get('products.json')
      .then((response) => {
        this.products = response.data.products;
      });
  }
};
</script>

<style scoped>
  .bounce-enter-active {
    display: inline-block;
    animation: shake 0.72s cubic-bezier(.37,.07,.19,.97) both;
    transform: translate3d(0, 0, 0);
    backface-visibility: hidden;
  }
  @keyframes shake {
    10%, 90% {
      color: red;
      transform: translate3d(-1px, 0, 0);
    }
    20%, 80% {
      transform: translate3d(2px, 0, 0);
    }
    30%, 50%, 70% {
      color: red;
      transform: translate3d(-4px, 0, 0);
    }
    40%, 60% {
      transform: translate3d(4px, 0, 0);
    }
  }
</style>