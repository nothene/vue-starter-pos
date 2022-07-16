<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import Products from './components/Products.vue';
import Recipes from './components/Recipes.vue';
import Home from './components/Home.vue';
import NotFound from './components/NotFound.vue';
import Production from './components/Production.vue';
import Prices from './components/Prices.vue';
import Purchase from './components/Purchase.vue';
import Sell from './components/Sell.vue';
import axios from 'axios';

let companies = reactive([]);
let products = reactive([]);
let recipes = reactive([]);

const routes = 
{
    //'/': {name: 'Home', component: Home},
    '/': {name: 'Products', component: Products},
    '/recipes':  {name: 'Recipes', component: Recipes},
    '/production':  {name: 'Production', component: Production},
    '/purchase':  {name: 'Purchase', component: Purchase},
    '/sell':  {name: 'Sell', component: Sell},
};

const currentPath = ref(window.location.hash);

window.addEventListener('hashchange', () => {
    currentPath.value = window.location.hash;
});

const currentView = computed(() => {
    return routes[currentPath.value.slice(1) || '/'].component || NotFound;
});

function print(){
    console.log(currentPath.value);
}

watchEffect(async () => { 
    await axios.get('http://localhost:8000/companies').then(
        function(response){
            companies.push(response.data);
            //console.log(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
});

watchEffect(async () => {
    await axios.get('http://localhost:8000/products').then(
        function(response){
            products.push(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
})

watchEffect(async () => { 
    await axios.get('http://localhost:8000/companies').then(
        function(response){
            companies.push(response.data);
            //console.log(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
});

watchEffect(async () => {
    await axios.get('http://localhost:8000/recipes').then(
        function(response){
            recipes.push(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
})

</script>

<template>
  <div class="container-fluid">
    <header class="d-flex flex-wrap justify-content-center py-3 mb-4 border-bottom">
      <a href="/" class="d-flex align-items-center mb-3 mb-md-0 me-md-auto text-dark text-decoration-none">
        <span class="fs-4">POS Dashboard</span>
      </a>

      <ul class="nav nav-pills">
        <li class="nav-item" v-for="(value, index) in routes" ref="methodsRef" :key="index">
          <a :href="'#' + index" class="nav-link" :class="{ active: (currentPath.slice(1) || '/') == index }" @click="print" :id="index">{{value['name']}}</a>
        </li>
      </ul>
    </header>

    <!-- <button class="btn btn-primary" @click="print">Click</button> -->

    <component :is="currentView" :companies="companies[0]" :products="products[0]" :recipes="recipes[0]"/>
  </div>
</template>

<style>

</style>