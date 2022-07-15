<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateProduct from './CreateProduct.vue';
import CannotDelete from './CannotDelete.vue';
import DeleteOk from './DeleteOk.vue';
import axios from 'axios';

let products = reactive([]);

let companiesMap = computed(() => {
    return props.companies.reduce(
        (prev, cur) => (
            prev[cur.ID] = cur.name
        ), {});
});

let companyID = ref(0);
let isRaw = ref(2);
let curModal = ref(0);

let props = defineProps([
    'companies'
]);

const routes = 
{
    '/create': {name: 'Home', component: CreateProduct}
};


watchEffect(async () => {
    await axios.get('http://localhost:8000/products')
        .then(function(response) {
            products.push(response.data);
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        });      
    }
);

async function getProduct(){
    console.log('product refreshing...');
    await axios.get('http://localhost:8000/products')
    .then(function(response) {
        products.push(response.data[0]);
        console.log(response.data[0]);
    })
    .catch(function (error) {
        console.log(error);
    }).then(function() {
    
    });         
}

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

function deleteProduct(id){
    axios.delete(`http://localhost:8000/products/${id}`)
        .then(function(response) {
            console.log(response.data);
            getProduct();
            curModal = DeleteOk;
            console.log(curModal);
        })
        .catch(function (error) {            
            console.log(error);
            curModal = CannotDelete;
            console.log(curModal);
        }).then(function() {
        
    });
}

let showCreate = ref(false);

function showEditForm(id){

}

function print(){
}

let actionPointer = reactive({
    id: 0,
    action: ''
});

function setActionPointer(id, action){
    actionPointer.id = id;
    actionPointer.action = action;
}

</script>

<template>
    <div class="container-fluid mb-3">
        <div class="row align-items-center p-3 bg-light">
            <div class="lead col" style="">
                Products
            </div>      
            <div class="col-2">            
                <div v-if="!showCreate">
                    <button class="btn btn-primary" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate; print()">Create Product</button>
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
                    <CreateProduct :companies="props.companies" @refresh-product="getProduct()"/>
                </div>
            </div>
        </div>
    </div>

    <div class="bg-light p-1 mb-2">
        <div class="p-2">
            <div class="mb-1">
                Filter by:
                <span v-if="companyID > 0">Company </span>
                <span v-if="isRaw < 2">Rawness</span>
            </div>
            <div class="p-1">
                <span>Company Name:</span>
                <select class="form-select" v-model="companyID">
                    <option selected value=0>No Filter</option>
                    <option v-for="(value, index) in companies" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>
            <div class="p-1">
                <span>Is Raw Product:</span>
                <select class="form-select" v-model="isRaw">
                    <option selected value=2>No Filter</option>
                    <option value=0>No</option>
                    <option value=1>Yes</option>
                </select>
            </div>  
        </div>      
    </div>        

    <ul class="list-group">
        <template v-for="(value, index) in products[0]" :key="value.ID" >
            <li v-if="(companyID == 0 || companyID == value.company_id) && (value.is_raw_material == isRaw || (isRaw == 2 ? true : false))" class="list-group-item">
                <div class="row align-items-center">
                    <div class="row">
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
                            <!-- <a class="btn btn-primary m-1" @click="getDetail(value.ID)">Detail</a> -->
                            <a class="btn btn-primary m-1" @click="getPriceList(value.ID); setActionPointer(value.ID, 'price');">Price List</a>
                            <template v-if="value.recipe_id">
                                <a class="btn btn-primary m-1" @click="getRecipe(value.ID); setActionPointer(value.ID, 'recipe');">Recipe</a>                
                            </template>
                            <a class="btn btn-primary m-1" @click="setActionPointer(value.ID, 'edit');">Edit</a>
                            <a class="btn btn-danger m-1" @click="deleteProduct(value.ID)" data-bs-toggle="modal" data-bs-target="#staticBackdrop">Delete</a>
                        </div>
                    </div>
                    <template v-if="actionPointer.id==value.ID">
                        <template v-if="actionPointer.action=='price'">
                            <div class="row">
                                <div class="card m-2">
                                    <div class="card-body">
                                        <h5 class="card-title">Price List</h5>
                                        <p class="card-text"></p>
                                    </div>
                                </div>
                            </div>   
                        </template>
                        <template v-else-if="actionPointer.action=='recipe'">
                            <div class="row">
                                <div class="card m-2">
                                    <div class="card-body">
                                        <h5 class="card-title">Recipe</h5>
                                        <p class="card-text">Methods:</p>
                                    </div>
                                </div>
                            </div>  
                        </template> 
                        <template v-else-if="actionPointer.action=='edit'">
                            <div class="row">
                                <div class="card m-2">
                                    <div class="card-body">
                                        <h5 class="card-title">Edit Form</h5>
                                        <p class="card-text"></p>
                                    </div>
                                </div>
                            </div>  
                        </template>                    
                    </template>
                </div>
            </li>            
        </template>
    </ul>

    <!-- <component :is="curModal" /> -->
    <CannotDelete/>

</template>

<style>

</style>