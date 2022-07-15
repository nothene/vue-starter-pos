<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateRecipe from './CreateRecipe.vue';
import axios from 'axios';

let recipes = reactive([]);
let recipeDetail = reactive({});
let companies = reactive([]);
let showCreate = ref(false);

let props = defineProps([
    'companies', 'products'
]);

let actionPointer = reactive({
    id: 0,
    action: ''
});

function setActionPointer(id, action){
    actionPointer.id = id;
    actionPointer.action = action;
}

watchEffect(async () => {
    await axios.get('http://localhost:8000/recipes')
        .then(function(response) {
            recipes.push(response.data);
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
        });         
    }
);

function getDetail(id){
    axios.get(`http://localhost:8000/products/${id}`)
        .then(function(response) {
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
    }); 
}

function getPriceList(id){
    axios.get(`http://localhost:8000/price?product_id=${id}`)
        .then(function(response) {
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
    }); 
}

function getRecipeDetail(id){
    axios.get(`http://localhost:8000/recipes/${id}`)
        .then(function(response) {
            recipeDetail[id] = response.data;
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
    });
}

function getRawMaterials(){
    if(props.products){
        let a = props.products;
        //console.log('here', a.filter(x => x.is_raw_material));
        a = a.filter(x => x.is_raw_material);
        return a;
    } else {
        return [];
    }
}

</script>

<template>
    <div class="container-fluid mb-3">
        <div class="row align-items-center p-3 bg-light">
            <div class="lead col" style="">
                Recipes
            </div>      
            <div class="col-2">            
                <div v-if="!showCreate">
                    <button class="btn btn-primary" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">Create Recipe</button>
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
                    <CreateRecipe :companies="props.companies" :products="getRawMaterials()"/>
                </div>
            </div>
        </div>
    </div>
    <ul class="list-group">
        <li v-for="(value, index) in recipes[0]" :key="value.ID" class="list-group-item">
            <div class="row align-items-center">
                <div class="col">
                    <span style="font-weight: normal" class="lead">
                        {{value.name}}    
                    </span>
                </div>
                <div class="col">
                    <a class="btn btn-primary m-1" @click="getRecipeDetail(value.ID); setActionPointer(value.ID, 'recipe')">Show Recipe</a>
                    <a class="btn btn-primary m-1" @click="setActionPointer(value.ID, 'edit')">Edit</a>
                    
                    <a class="btn btn-danger m-1" @click="setActionPointer(value.ID, 'delete')">Delete</a>
                </div>
            </div>
        </li>
    </ul>
</template>

<style>

</style>