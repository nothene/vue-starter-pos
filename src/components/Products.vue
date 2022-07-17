<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateProduct from './CreateProduct.vue';
import StockOnhands from './StockOnhands.vue';
import EditProduct from './EditProduct.vue'
import axios from 'axios';
import PopupModal from './PopupModal.vue';
import Prices from './Prices.vue';

let productForm = reactive({
    company_id: 1,
    code: '',
    name: '',
    color: '',
    is_raw_material: true,
    is_active: true,
    uom_name: '',
    recipe_id: 1  
});

//let products = reactive([]);
let productPrices = reactive({});
let productOnhands = reactive({});

let showCreate = ref(false);
let filters = reactive({
    companyID: 0,
    isRaw: 2,
    hasRecipe: 2,
});

let props = defineProps([
    'companies',
    'products',
    'recipes',
]);

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
})

// watchEffect(async () => {
//     await axios.get('http://localhost:8000/products')
//         .then(function(response) {
//             products.push(response.data);
//             //console.log(response.data);
//         })
//         .catch(function (error) {
//             console.log(error);
//         });      
//     }
// );

async function getDetails(id){
    await axios.get(`http://localhost:8000/products/${id}`)
        .then(function(response) {
            productPrices['fetched'] = true;
            productPrices[id] = response.data[1];
            productOnhands[id] = response.data[2];
            console.log(productOnhands[id]);
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
            window.location.reload();
        })
        .catch(function (error) {
            alert(JSON.stringify(error.response.data.message));
            console.log(error.response.data.message);
        }).then(function() {
        
    });
}

function passFilter(value){
    if((filters['companyID'] == value.company_id || filters['companyID'] == 0) 
        && (value.is_raw_material == filters['isRaw'] || (filters['isRaw'] == 2 ? true : false))
        && ((value.recipe_id ? true : false) == filters['hasRecipe'] || (filters['hasRecipe'] == 2 ? true : false)))
    {
        return true;
    }
    return false;
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
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">Create Product</button>
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
                    <CreateProduct :companies="props.companies"/>
                </div>
            </div>
        </div>
    </div>

    <div class="bg-light p-1 mb-2">
        <div class="p-2">
            <div class="mb-1">
                Filter by:
                <span v-if="filters['companyID'] > 0" class="badge bg-primary rounded-pill p-2 me-1">Company</span>
                <span v-if="filters['isRaw'] < 2" class="badge bg-primary rounded-pill p-2 me-1">Is Raw</span>
                <span v-if="filters['hasRecipe'] < 2" class="badge bg-primary rounded-pill p-2 me-1">Has Recipe</span>
            </div>
            <div class="p-1">
                <span>Company Name:</span>
                <select class="form-select" v-model="filters['companyID']">
                    <option selected value=0>No Filter</option>
                    <option v-for="(value, index) in companies" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>
            <div class="p-1">
                <span>Is Raw Product:</span>
                <select class="form-select" v-model="filters['isRaw']">
                    <option selected value=2>No Filter</option>
                    <option value=0>No</option>
                    <option value=1>Yes</option>
                </select>
            </div>  
            <div class="p-1">
                <span>Has Recipe:</span>
                <select class="form-select" v-model="filters['hasRecipe']">
                    <option selected value=2>No Filter</option>
                    <option value=0>No</option>
                    <option value=1>Yes</option>
                </select>
            </div>            
        </div>      
    </div>        

    <ul class="list-group">
        <template v-for="(value, index) in props.products" :key="value.ID" >
            <li v-if="passFilter(value)" class="list-group-item">
                <div class="row align-items-center">
                    <div class="row">
                        <div class="col">
                            <span style="font-weight: normal" class="lead">
                                {{value.name}}    
                            </span>
                            <div>
                                <label>Code: </label>{{" " + value.code}}
                            </div>                            
                            <div>
                                <label>Product Type: </label>{{value.is_raw_material ? " Raw Material" : " Sellable"}}
                            </div>                            
                            <div v-if="value.color != null">
                                <label>Color: </label>{{" " + value.color}}
                            </div>                             
                            <div>
                                <label>Is Active: </label>{{value.is_active ? " Yes" : " No"}}
                            </div>                            
                            <div>
                                <label>Unit of Measure: </label>{{" " + value.uom_name}}
                            </div>
                            <div v-if="!value.is_raw_material">
                                <label>Recipe: </label>{{" " + recipesMap[value.recipe_id]}}
                            </div>
                        </div>
                        <div class="col">
                            <button class="btn btn-primary m-1" data-bs-toggle="collapse" :data-bs-target="'#onhand' + value.ID" @click="getDetails(value.ID)">Stocks Onhand</button>
                            <button class="btn btn-primary m-1" :class="{disabled: (value.is_raw_material == true)}"
                                data-bs-toggle="collapse" :data-bs-target="'#priceList' + value.ID" @click="getDetails(value.ID); print()">Price List & Publish</button>
                            <button class="btn btn-primary m-1" data-bs-toggle="collapse" :data-bs-target="'#edit' + value.ID">Edit</button>
                            <button class="btn btn-danger m-1" @click="deleteProduct(value.ID)" data-bs-toggle="modal" data-bs-target="#staticBackdrop">Delete</button>
                        </div>
                    </div>
                    <div class="row align-items-center">
                        <div class="collapse" :id="'onhand' + value.ID">
                            <template v-if="productOnhands[value.ID]">
                                <StockOnhands :stockOnhands="productOnhands[value.ID]" :companiesMap="companiesMap"/>
                            </template>
                        </div>
                        <div class="collapse" :id="'priceList' + value.ID">
                            <template v-if="productPrices[value.ID]">
                                <Prices :priceList="productPrices[value.ID]" :productId="value.ID" :isRaw="value.is_raw_material" :companiesMap="companiesMap"/>
                            </template>
                        </div>
                        <div class="collapse" :id="'edit' + value.ID">
                            <EditProduct :companies="props.companies" :productForm="value" :recipes="props.recipes"/>
                        </div>                        
                    </div>                
                </div>
            </li>            
        </template>
    </ul>

    <!-- <template v-if="true">
        <PopupModal />
    </template> -->
</template>

<style>

</style>