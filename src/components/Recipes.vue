<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateProduct from './CreateProduct.vue';
import axios from 'axios';

let recipes = reactive([]);
let companies = reactive([]);

const routes = 
{
    '/create': {name: 'Home', component: CreateProduct}
};


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

function getRecipe(id){
    axios.get(`http://localhost:8000/recipes/${id}`)
        .then(function(response) {
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
    });
}

function print(){
    console.log(recipes[0][1]);
}

</script>

<template>
    <div class="container-fluid mb-2 p-2 border">
        Filter by: 
        <button class="btn btn-primary" @click="print">Click</button>
    </div>
    <ul class="list-group">
        <li v-for="(value, index) in recipes[0]" :key="value.ID" class="list-group-item">
            <div class="row align-items-center">
                <div class="col">
                    <span style="font-weight: normal" class="lead">
                        {{value.name}}    
                    </span>
                    <div>
                        <label style="color: black">Product Type: </label>{{value.is_raw_material ? " Raw Material" : " Sellable Product"}}
                    </div>
                    <div>
                        <label>Unit of measure: </label>{{" " + value.uom_name}}
                    </div>
                </div>
                <div class="col">
                    <a class="btn btn-primary m-1" @click="getDetail(value.ID)">Toggle Detail</a>
                    <a class="btn btn-primary m-1" @click="getPriceList(value.ID)">Edit</a>
                    <a class="btn btn-danger m-1" @click="getPriceList(value.ID)">Delete</a>
                </div>
            </div>
        </li>
    </ul>
</template>

<style>

</style>