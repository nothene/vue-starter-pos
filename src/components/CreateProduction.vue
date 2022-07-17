<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let productionForm = reactive({
    company_id: 1,
    production_date: '',
    recipe_id: 1,
    qty_produced: 0,
    date: '',
    time: '',    
});

let props = defineProps([
    'companies',
    'recipes'
]);

let publishAt = ref("now");
let currentDateTime = reactive({date: new Date()});

let selectedDateTime = reactive({date: new Date(), time: new Date()});

setInterval(() => {
    let now = new Date();
    currentDateTime.date = new Date();
}, 500);

async function createProduction(){
    if(publishAt.value == "now"){
        let d = new Date();
        console.log(d.toLocaleString());
        productionForm['production_date'] = d.toLocaleString();
    } else {
        productionForm['production_date'] = productionForm['date'] + " " + productionForm['time'];
    }
    console.log(productionForm);
    await axios.post(`http://localhost:8000/productions`, productionForm)
    .then(function(response) {
        //console.log(response.data);
        alert(response.data);
        window.location.reload();
    })
    .catch(function (error) {
        console.log(error);
        alert(error.response.data);
    }).then(function() {
        
    }); 
}

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="lead card-title">Create Production</div>
                <div class="mb-3">
                    <label class="form-label">Company</label>
                    <select class="form-select" v-model="productionForm.company_id">
                        <option v-for="(value, index) in companies" :key="value.ID" :value="value.ID">
                            {{value.name}}
                        </option>
                    </select>
                </div>
                <div class="mb-3">
                    <label class="form-label">Recipe/Product</label>
                    <select class="form-select" v-model="productionForm.recipe_id">
                        <option v-for="(value, index) in recipes" :key="value.ID" :value="value.ID">
                            {{value.name}}
                        </option>
                    </select>
                </div>
                <div class="mb-3">
                    <label class="form-label">Quantity</label>
                    <input type="number" class="form-control" v-model="productionForm['qty_produced']">
                </div>                
                <div class="mb-3">
                    <div class="mb-3">
                        <label class="form-label">Produce at: {{publishAt == "now" ? currentDateTime.date.toLocaleString() : "manually selected below"}}</label>            
                        <select class="form-select" v-model="publishAt">
                            <option value="now" selected>Now</option>
                            <option value="select">Select Date</option>
                        </select>  
                    </div>
                    <div>
                        <template v-if="publishAt == 'select'">
                            <label class="form-label">Date & Time</label>
                            <input type="date" class="form-control" v-model="productionForm['date']">     
                            <input type="time" class="form-control" v-model="productionForm['time']">        
                            <label class="form-label">UTC+7</label>
                        </template> 
                    </div>                  
                </div>                
                <button type="submit" class="btn btn-primary" @click="createProduction();">Submit</button>
        </div>
    </div> 
</template>