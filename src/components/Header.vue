<template>
  <header>
    <div class="navbar navbar-light bg-light border border-dark rounded mb-4">
      <div class="float-md-left">
        <h1>
          <router-link :to="{name: 'iMain'}">
            {{ sitename }}
          </router-link>
        </h1>
      </div>
      <div class="nav navbar-nav ml-auto cart">
        <router-link
          active-class="active"
          tag="button"
          class="btn btn-secondary btn-lg"
          :to="{name: 'Form'}"
        >
          <span class="fa fa-shopping-cart">{{ cartItemCount }}</span>
          Checkout
        </router-link>
      </div>
    </div>
  </header>
</template>

<script>
  export default {
    name: 'my-header',
    data() {
      return {
        sitename: "Vue.js Pet Depot"
      }
    },
    props: ['cartItemCount']
  }
</script>

<style scoped>
  a {
    text-decoration: none;
    color: black;
  }
  
  .router-link-exact-active {
    color: yellowgreen !important;
  }
</style>