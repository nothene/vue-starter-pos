<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let props = defineProps([
    'companiesMap',
    'products',
]);

let purchaseableType = ref([]);

let purchaseForm = reactive({
    company_id: 1,
    transaction_date: '',
    supplier_name: '',
    is_cancelled: false,
    details: [],
    notes: ''
});

function addProduct(){
    purchaseForm['details'].push({
        raw_material_id: 0,
        qty: 0,
        price: 0,
        // in fractions, take care to convert
        disc_1: 0,
        disc_2: 0
    });
    purchaseableType.push('raw');
}

function popProduct(){
    purchaseForm['details'].pop();
    purchaseableType.pop();
}

async function createPurchase(){
    let arr = [];
    let ok = true;
    //console.log(purchaseForm);
    // regex is better
    for(var i = 0; i < purchaseForm['details'].length; i++){
        if(purchaseForm['details'][i].qty <= 0 || purchaseForm['details'][i].disc_1 < 0 || purchaseForm['details'][i].disc_2 < 0){
            ok = false;
            break;
        }
    }
    if(!ok){
        alert('Negative or zero quantity detected');
        return;
    } else{
        for(var i = 0; i < purchaseForm['details'].length; i++){
            purchaseForm['details'][i].disc_1 /= 100;
            purchaseForm['details'][i].disc_2 /= 100;
            arr.push(purchaseForm['details'][i]);
        }        
        purchaseForm['details'] = arr;
    }
    //console.log(purchaseForm);
    await axios.post('http://localhost:8000/purchase', purchaseForm)
        .then(function(response){
            alert("Purchase made successfully", response.data);
            Object.assign(purchaseForm, {
                company_id: 1,
                transaction_date: '',
                supplier_name: '',
                is_cancelled: false,
                details: [],
                notes: ''
            })            
            window.location.reload();
        }).catch(function(error){
            console.log(error.data);
            alert(error.response.data.message);
        });
}

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="card-title lead">Create Purchase</div>
            <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="purchaseForm.company_id">
                    <option v-for="(value, index) in companiesMap" :key="index" :value="index">
                        {{value}}
                    </option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Purchase Date</label>           
                <input type="date" class="form-control" v-model="purchaseForm['transaction_date']">        
            </div>               
            <div class="mb-3">
                <label class="form-label">Supplier Name</label>
                <input type="text" class="form-control" v-model="purchaseForm['supplier_name']">
            </div>            
            <div class="mb-3">
                <label class="form-label">Is Cancelled</label>
                <select class="form-select" v-model="purchaseForm['is_cancelled']">
                    <option :value="true" selected>Yes</option>
                    <option :value="false" selected>No</option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Notes</label>
                <input type="text" class="form-control" v-model="purchaseForm['notes']">
            </div>
            <div class="mb-3">
                    <label class="form-label">Add Products to Purchase</label>
                    <div v-for="(value, index) in purchaseForm['details']">
                        <div class="mb-2">
                            <div class="input-group">
                                <label class="input-group-text">Type</label>
                                <select class="form-select" v-model="purchaseableType[index]">
                                    <option value="retail">Retail</option>
                                    <option value="raw" selected>Raw Ingredient</option>
                                </select>
                                <label class="input-group-text">Name</label>
                                <template v-if="purchaseableType[index] == 'raw'">
                                    <select class="form-select" v-model="purchaseForm['details'][index].raw_material_id">
                                        <template v-for="(value2, index2) in products" :key="value2.ID">
                                            <option v-if="value2.is_raw_material" :value="value2.ID">
                                                {{value2.name}} {{'(' + value2.uom_name + ')'}}
                                            </option>
                                        </template>
                                    </select>
                                </template>
                                <template v-else>
                                    <select class="form-select" v-model="purchaseForm['details'][index].raw_material_id">
                                        <template v-for="(value3, index3) in products" :key="value3.ID">
                                            <option v-if="!value3.is_raw_material && !value3.recipe_id" :value="value3.ID">
                                                {{value3.name}} {{'(' + value3.uom_name + ')'}}
                                            </option>                                    
                                        </template>
                                    </select>                                
                                </template>
                                    <label class="input-group-text">Quantity</label>
                                    <input type="number" class="form-control" v-model="purchaseForm['details'][index].qty">                                
                            </div>       
                            <div class="input-group mb-3">
                                <label class="input-group-text">Price</label>
                                <input type="number" min="0" class="form-control" v-model="purchaseForm['details'][index].price">
                                <label class="input-group-text">% Discount 1</label>
                                <input type="number" class="form-control" v-model="purchaseForm['details'][index].disc_1">
                                <label class="input-group-text">% Discount 2</label>                            
                                <input type="number" class="form-control" v-model="purchaseForm['details'][index].disc_2">                      
                            </div>          
                        </div>
                    </div>
                    <!-- <span>{{purchaseForm['details']}}</span> -->
                    <div>
                        <button class="btn btn-primary me-2" @click="addProduct()">Add Product</button>
                        <button class="btn btn-danger" @click="popProduct()">Remove Last Product</button>
                    </div>
                </div>     
                <button type="submit" class="btn btn-primary" @click="createPurchase();">Submit</button>                             
        </div>
    </div>
</template>