<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateProduction from './CreateProduction.vue';
import HeaderLayout from './HeaderLayout.vue';
import axios from 'axios';

let props = defineProps([
    'companies',
    'products',
    'recipes'
]);

let productsMap = computed(() => {
    if(!props.companies){
        return null;
    }
    let arr = JSON.parse(JSON.stringify(props.products));
    let a = {};
    for(var i = 0; i < arr.length; i++){
        a[arr[i].ID] = arr[i].name;
    }    
    return a;    
})

let productions = reactive([]);
let showCreate = ref(false);

let companiesMap = computed(() => {
    if(!props.companies){
        return null;
    }
    let arr = JSON.parse(JSON.stringify(props.companies));
    let a = {};
    for(var i = 0; i < arr.length; i++){
        a[arr[i].ID] = arr[i].name;
    }    
    return a;
});

let recipesMap = computed(() => {
    if(!props.recipes){
        return null;
    }    
    let arr = JSON.parse(JSON.stringify(props.recipes));
    let a = {};
    for(var i = 0; i < arr.length; i++){
        a[arr[i].ID] = arr[i].name;
    }    
    return a;
});

let produceables = computed(() => {
    if(!props.products){
        return null;
    }    
    let arr = JSON.parse(JSON.stringify(props.products));
    return arr.filter((x) => !x.is_raw_material && x.recipe_id);    
})

watchEffect(async () => {
    await axios.get('http://localhost:8000/productions').then(
        function(response){
            productions.push(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
})

</script>

<template>
    <div class="container-fluid mb-3">
        <div class="row align-items-center p-3 bg-light">
            <div class="lead col" style="">
                Production
            </div>      
            <div class="col-2">            
                <div v-if="!showCreate">
                    <button class="btn btn-primary" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">Create Production</button>
                </div>
                <div v-else-if="showCreate">
                    <button class="btn btn-danger" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">
                        Close
                    </button>
                </div>
            </div>            
        </div>
        <div class="row align-items-center justify-content-left bg-light p-1">
            <div class="col">
                <div class="collapse" id="collapse1">
                    <CreateProduction :recipesMap="recipesMap" :companies="companies" :products="produceables"/>
                </div>
            </div>
        </div>
    </div>
    <table class="table table-bordered table-hover">
        <thead>
            <tr>
                <th scope="col">ID</th>
                <th scope="col">Product Name</th>
                <th scope="col">Company</th>
                <th scope="col">Production Date</th>
                <th scope="col">Quantity</th>
            </tr>
        </thead>
        <tbody>
            <template v-for="(value, index) in productions[0]" :key="value.ID">
                <tr>
                    <th scope="row">{{value.ID}}</th>
                    <td>{{productsMap[value.product_id]}}</td>    
                    <td>{{companiesMap[value.company_id]}}</td>
                    <td>{{value.production_date}}</td>                                              
                    <td>{{value.qty_produced}}</td>
                </tr>
            </template>        
        </tbody>
    </table>
</template>