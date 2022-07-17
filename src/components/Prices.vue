<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let props = defineProps([
    'productId',
    'priceList',
    'companiesMap',
    'isRaw'
])

let priceForm = reactive({
    product_id: props.productId,
    date: '',
    time: '',
});

async function publishPrice(){
    if(publishAt.value == "now"){
        let d = new Date();
        console.log(d.toLocaleString());
        priceForm['published_at'] = d.toLocaleString();
    } else {
        priceForm['published_at'] = priceForm['date'] + " " + priceForm['time'];
    }
    await axios.post(`http://localhost:8000/prices`, priceForm)
    .then(function(response) {
        console.log(response);
        window.location.reload();
    })
    .catch(function (error) {
        console.log(error);
    }).then(function() {
        
    }); 
}

let publishAt = ref("now");
let currentDateTime = reactive({date: new Date()});

let selectedDateTime = reactive({date: new Date(), time: new Date()});

setInterval(() => {
    let now = new Date();
    currentDateTime.date = new Date();
}, 500);

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="mb-3">
                <div class="lead card-title">Publish Price</div>
                <label class="form-label">Company Name</label>            
                <select class="form-select" v-model="priceForm['company_id']">
                    <option v-for="(value, index) in companiesMap" :key="index" :value="Number(index)">
                        {{value}}
                    </option>
                </select>            
            </div>
            <div class="mb-3">
                <label class="form-label">Publish price at: </label>  
                <select class="form-select mb-3" v-model="publishAt">
                    <option value="now" selected>Now</option>
                    <option value="select">Select Date</option>
                </select>
                <div v-if="publishAt == 'select'">
                    <label class="form-label">Date & Time</label>
                    <div class="input-group mb-3">    
                        <input type="date" class="form-control" v-model="priceForm['date']">     
                        <input type="time" class="form-control" v-model="priceForm['time']">                    
                    </div>
                </div>                
            </div>
            <label class="form-label">Price</label>
            <div class="input-group mb-3">                
                <span class="input-group-text">Rp</span>
                <input type="number" class="form-control" v-model="priceForm['price']">
            </div>
            <button type="button" @click="publishPrice()" class="btn btn-primary mt-1">Publish price</button>
        </div>
    </div>    
    <template v-if="!isRaw">
        <div class="card m-2">
            <div class="card-body">
                <div class="lead card-title">Price List</div>        
                    <ul class="list-group" v-for="(value, index) in priceList">
                        <li class="list-group-item">
                            <div>{{"Company: " + companiesMap[value.company_id]}}</div>
                            <div>{{"Price: Rp. " + value.price}}</div>
                            <div>{{"Published at: " + value.published_at}}</div>
                        </li>
                    </ul>
            </div>
        </div>
    </template>
</template>