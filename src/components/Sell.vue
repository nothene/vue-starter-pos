<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';
import CreateSell from './CreateSell.vue';

let props = defineProps([
    'companies',
    'products',
    'recipes'
]);

let idr = Intl.NumberFormat('id-ID', {
    style: 'currency',
    currency: 'IDR'
});

let sell = reactive([]);
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

let sellables = computed(() => {
    if(!props.products){
        return null;
    }    
    let arr = JSON.parse(JSON.stringify(props.products));
    return arr.filter((x) => (!x.is_raw_material && !x.recipe_id) || (!x.is_raw_material && x.recipe_id));
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
    await axios.get('http://localhost:8000/sell').then(
        function(response){
            sell.push(response.data);
        }
    ).catch(function(error){
        console.log(error);
        //alert(error.response);
    })
})

</script>

<template>
    <div class="container-fluid mb-3">
        <div class="row align-items-center p-3 bg-light">
            <div class="lead col" style="">
                Sell
            </div>      
            <div class="col-2">            
                <div v-if="!showCreate">
                    <button class="btn btn-primary" type="button" data-bs-toggle="collapse" data-bs-target="#collapse1" 
                    aria-expanded="false" aria-controls="collapse1" @click="showCreate = !showCreate;">Create Sell</button>
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
                    <CreateSell :companiesMap="companiesMap" :products="sellables"/>
                </div>
            </div>
        </div>
    </div>
    <table class="table table-bordered table-hover">
        <thead>
            <tr>
                <th scope="col">ID</th>
                <th scope="col">Company</th>
                <th scope="col">Customer</th>
                <th scope="col">Cancelled at</th>
                <th scope="col">Printed at</th>
                <th scope="col">Transaction Date</th>
                <th scope="col">Subtotal</th>
                <th scope="col">Discount</th>
                <th scope="col">Grand Total</th>
            </tr>
        </thead>
        <tbody>
            <template v-for="(value, index) in sell[0]" :key="value.ID">
                <tr>
                    <th scope="row">{{value.ID}}</th>
                    <td>{{companiesMap[value.company_id]}}</td>
                    <td>{{value.customer_name}}</td>
                    <td>{{value.is_cancelled ? (new Date(value.cancelled_at)).toDateString() : "-"}}</td>
                    <td>{{value.is_printed ? (new Date(value.printed_at)).toDateString() : "-"}}</td>
                    <td>{{(new Date(value.transaction_date)).toDateString()}}</td>                                              
                    <td>{{idr.format(value.sub_total)}}</td>
                    <td>{{idr.format(value.disc_amount)}}</td>
                    <td>{{idr.format(value.grand_total)}}</td>
                </tr>
            </template>        
        </tbody>
    </table>
</template>
