<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CreateProduction from './CreateProduction.vue';
import axios from 'axios';
import CreateRecipe from './CreateRecipe.vue';
import CreatePurchase from './CreatePurchase.vue';

let props = defineProps([
    'companies',
    'products',
    'recipes'
]);

let idr = Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR'
});

let productions = reactive([]);
let purchase = reactive([]);
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

watchEffect(async () => {
    await axios.get('http://localhost:8000/purchase').then(
        function(response){
            purchase.push(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error.response);
    })
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
                Purchase
            </div>      
            <div class="col-2">            
                <div v-if="!showCreate">
                    <button class="btn btn-primary" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">Create Purchase</button>
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
                    <CreatePurchase :companiesMap="companiesMap" :products="products"/>
                </div>
            </div>
        </div>
    </div>
    <table class="table table-bordered table-hover">
        <thead>
            <tr>
                <th scope="col">ID</th>
                <th scope="col">Company</th>
                <th scope="col">Supplier</th>
                <th scope="col">Cancelled</th>
                <th scope="col">Transaction Date</th>
                <th scope="col">Subtotal</th>
                <th scope="col">Discount</th>
                <th scope="col">Grand Total</th>
            </tr>
        </thead>
        <tbody>
            <template v-for="(value, index) in purchase[0]" :key="value.ID">
                <tr>
                    <th scope="row">{{value.ID}}</th>
                    <td>{{companiesMap[value.company_id]}}</td>
                    <td>{{value.supplier_name}}</td>
                    <td>{{value.is_cancelled}}</td>
                    <td>{{(new Date(value.transaction_date)).toDateString()}}</td>                                              
                    <td>{{idr.format(value.sub_total)}}</td>
                    <td>{{idr.format(value.disc_amount)}}</td>
                    <td>{{idr.format(value.grand_total)}}</td>
                </tr>
            </template>        
        </tbody>
    </table>
</template>
