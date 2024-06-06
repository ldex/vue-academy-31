<script setup>
import { ref } from 'vue'
import ProductService from './services/ProductService.js'
import { useUserStore } from '@/stores/user';

const userStore = useUserStore();

userStore.checkPreviousLogin();

let products = ref([])
const errorMessage = ref(null)
const isLoading = ref(false)

isLoading.value = true;
ProductService.getProducts()
  .then(data => products.value = data)
  .catch(error => {
    errorMessage.value = 'There was an error getting products from server, ' + error;
  })
  .finally(() => isLoading.value = false)
</script>

<template>
<div id="app">
    <h1>Vue Store</h1>
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/products">Products</router-link>
      <router-link to="/about">About</router-link>
      <router-link to="/admin">Admin</router-link>
      <router-link v-if="!userStore.isLoggedIn" to="/login">Login</router-link>
      <a v-else @click="userStore.logout()">Logout</a>
    </nav>
    <router-view v-slot="{ Component }">
      <transition name="page" mode="out-in">
        <component :is="Component" />
      </transition>
    </router-view>
    <hr />
    <footer>Copyright Vue Academy 2024</footer>
  </div>
</template>

<style scoped>
/* transitions */
.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
@keyframes acrossIn {
  0% { transform: translate3d(-100%, 0, 0); }
  100% { transform: translate3d(0, 0, 0); }
}

@keyframes acrossOut {
  0% { transform: translate3d(0, 0, 0); }
  100% { transform: translate3d(100%, 0, 0); }
}

.page-enter-active {
  animation: bounceIn .45s ease-out both;
}

.page-leave-active {
  animation: flipOutX .65s ease-in both;
}
</style>
