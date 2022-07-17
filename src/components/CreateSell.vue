<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let props = defineProps([
    'companiesMap',
    'products',
]);

let sellForm = reactive({
    company_id: 1,
    transaction_date: '',
    customer_name: '',
    is_cancelled: false,
    cancelled_at: '',
    is_printed: false,
    printed_at: '',
    details: [],
    notes: ''
});

function addProduct(){
    sellForm['details'].push({
        product_id: 1,
        qty: 0,
        // in fractions, take care to convert
        disc_1: 0,
        disc_2: 0
    });
}

function popProduct(){
    sellForm['details'].pop();
}

async function createSell(){
    let arr = [];
    let ok = true;
    for(var i = 0; i < sellForm['details'].length; i++){
        if(sellForm['details'][i].qty <= 0 || sellForm['details'][i].disc_1 < 0 || sellForm['details'][i].disc_2 < 0){
            ok = false;
            break;
        }
    }
    if(!ok){
        alert('Negative or zero quantity detected');
        return;
    } else{
        for(var i = 0; i < sellForm['details'].length; i++){
            sellForm['details'][i].disc_1 /= 100;
            sellForm['details'][i].disc_2 /= 100;
            arr.push(sellForm['details'][i]);
        }        
        sellForm['details'] = arr;
        // transaction must set date and time
        // transaction without a time will default to 00:00 (midnight at the start of the day)
        // might conflict with price publish
        sellForm['transaction_date'] = sellForm['date'] + " " + sellForm['time'];
    }
    //console.log(sellForm);
    await axios.post('http://localhost:8000/sell', sellForm)
        .then(function(response){
            alert("Sale made successfully", response.data);
            // Object.assign(sellForm, {
            //     company_id: 1,
            //     transaction_date: '',
            //     supplier_name: '',
            //     is_cancelled: false,
            //     details: [],
            //     notes: ''
            // })            
            window.location.reload();
        }).catch(function(error){
            alert(error.response.data.message);
        });
}

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="card-title lead">Create Sell</div>
            <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="sellForm.company_id">
                    <option v-for="(value, index) in companiesMap" :key="index" :value="index">
                        {{value}}
                    </option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Sell Date</label>           
                <div class="input-group mb-3">                    
                    <input type="date" class="form-control" v-model="sellForm['date']">
                    <input type="time" class="form-control" v-model="sellForm['time']">
                </div>
            </div>            
            <div class="mb-3">
                <label class="form-label">Customer Name</label>
                <input type="text" class="form-control" v-model="sellForm['customer_name']">
            </div>   
            <div class="mb-3">
                <label class="form-label">Is Cancelled</label>
                <select class="form-select" v-model="sellForm['is_cancelled']">
                    <option :value="true" selected>Yes</option>
                    <option :value="false" selected>No</option>
                </select>
            </div>
            <div class="mb-3" v-if="sellForm['is_cancelled']">
                <label class="form-label">Cancelled at</label>           
                <input type="date" class="form-control" v-model="sellForm['cancelled_at']">        
            </div>                        
            <div class="mb-3">
                <label class="form-label">Is Printed</label>
                <select class="form-select" v-model="sellForm['is_printed']">
                    <option :value="true" selected>Yes</option>
                    <option :value="false" selected>No</option>
                </select>
            </div>
            <div class="mb-3" v-if="sellForm['is_printed']">
                <label class="form-label">Printed at</label>           
                <input type="date" class="form-control" v-model="sellForm['printed_at']">        
            </div>                                 
            <div class="mb-3">
                <label class="form-label">Notes</label>
                <input type="text" class="form-control" v-model="sellForm['notes']">
            </div> 
            <div class="mb-3">
                    <label class="form-label">Add Products to Sell</label>
                    <div v-for="(value, index) in sellForm['details']">
                        <div class="mb-2">
                            <div class="input-group">
                            <select class="form-select" v-model="sellForm['details'][index].product_id">
                                <option v-for="(value2, index2) in products" :key="value2.ID" :value="value2.ID">
                                    {{value2.name}} {{'(' + value2.uom_name + ')'}}
                                </option>
                            </select>
                                <label class="input-group-text">Quantity</label>
                                <input type="number" class="form-control" v-model="sellForm['details'][index].qty">                                
                            </div>       
                            <div class="input-group mb-3">
                                <label class="input-group-text">% Discount 1</label>
                                <input type="number" class="form-control" v-model="sellForm['details'][index].disc_1">
                                <label class="input-group-text">% Discount 2</label>                            
                                <input type="number" class="form-control" v-model="sellForm['details'][index].disc_2">                      
                            </div>          
                        </div>
                    </div>
                    <!-- <span>{{sellForm['details']}}</span> -->
                    <div>
                        <button class="btn btn-primary me-2" @click="addProduct()">Add Item</button>
                        <button class="btn btn-danger" @click="popProduct()">Remove Last Item</button>
                    </div>
                </div>
            <button type="submit" class="btn btn-primary" @click="createSell();">Submit</button>              
        </div>
    </div>        
</template>