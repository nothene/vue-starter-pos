<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateRecipe from './CreateRecipe.vue';
import RecipeDetails from './RecipeDetails.vue';
import axios from 'axios';
import EditRecipe from './EditRecipe.vue';

let recipes = reactive([]);
let recipeDetails = reactive({});
let showCreate = ref(false);

let props = defineProps([
    'companies', 
    'products'
]);


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

function deleteRecipe(id){
    axios.delete(`http://localhost:8000/recipes/${id}`)
        .then(function(response) {
            console.log(response.data);
            window.location.reload();
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
    });     
}

function getRecipeDetail(id){
    axios.get(`http://localhost:8000/recipes/${id}`)
        .then(function(response) {
            recipeDetails[id] = response.data;
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

let rawMaterials = computed(() => {
    if(props.products){
        let a = JSON.parse(JSON.stringify(props.products));
        a = a.filter(x => x.is_raw_material);
        return a;
    }
});

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
                    <CreateRecipe :companies="props.companies" :products="rawMaterials"/>
                </div>
            </div>
        </div>
    </div>
    <ul class="list-group">
        <li v-for="(value, index) in recipes[0]" :key="value.ID" class="list-group-item">
            <div class="row align-items-center">
                <div class="col-6">
                    <span style="font-weight: normal" class="lead">
                        {{value.name}}    
                    </span>
                </div>
                <div class="col">
                    <a class="btn btn-primary m-1" data-bs-toggle="collapse" :data-bs-target="'#details' + value.ID" @click="getRecipeDetail(value.ID)">Show Details</a>
                    <a class="btn btn-primary m-1" data-bs-toggle="collapse" :data-bs-target="'#edit' + value.ID" @click="getRecipeDetail(value.ID)">Edit</a>
                    <a class="btn btn-danger m-1" @click="deleteRecipe(value.ID)">Delete</a>
                </div>
            </div>
            <div class="row align-items-center">
                <div class="collapse" :id="'details' + value.ID">
                    <RecipeDetails :details="recipeDetails[value.ID]" :notes="value.notes"/>
                </div>
                <div class="collapse" :id="'edit' + value.ID">
                    <EditRecipe :companies="props.companies" :products="rawMaterials" :recipeForm="value" :details="recipeDetails[value.ID]"/>
                </div>
            </div>
        </li>
    </ul>
</template>

<style>

</style>