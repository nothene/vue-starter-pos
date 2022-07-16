<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

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

let recipes = reactive([]);

let prop = defineProps([
    'companies'
])

watchEffect(async () => {
    await axios.get('http://localhost:8000/recipes')
        .then(function(response) {
            recipes.push(response.data);
            console.log(response.data);
        })
        .catch(function (error) {
            console.log(error);
        });      
    }
);

function createProduct(){
    axios.post(`http://localhost:8000/products`, productForm)
    .then(function(response) {
        console.log(response.data);
        alert('Product created successfully');
        Object.assign(productForm, {
            company_id: 1,
            code: '',
            name: '',
            color: '',
            is_raw_material: true,
            is_active: true,
            uom_name: '',
            recipe_id: 1  
        });
        window.location.reload();
    })
    .catch(function (error) {
        console.log(error);
        alert(error);
    }).then(function() {
    
    });
}

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="card-title">Create Product</div>
            <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="productForm.company_id">
                    <option v-for="(value, index) in prop.companies" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Code</label>
                <input type="text" class="form-control" v-model="productForm.code">
            </div>            
            <div class="mb-3">
                <label class="form-label">Name</label>
                <input type="text" class="form-control" v-model="productForm.name">
            </div>
            <div class="mb-3">
                <label class="form-label">Color</label>
                <input type="text" class="form-control" v-model="productForm.color">
            </div>
            <div class="mb-3">
                <label class="form-label">Is Raw Material</label>
                <select class="form-select" v-model="productForm.is_raw_material">
                    <option :value="true" selected>Yes</option>
                    <option :value="false" selected>No</option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Is Active</label>
                <select class="form-select" v-model="productForm.is_active">
                    <option :value="true" selected>Yes</option>
                    <option :value="false" selected>No</option>
                </select>
            </div> 
            <div class="mb-3">
                <label class="form-label">Unit of Measurement Name</label>
                <input type="text" class="form-control" v-model="productForm.uom_name">
            </div>   
            <div class="mb-3">
                <label class="form-label">Recipe</label>
                <select class="form-select" v-model="productForm.recipe_id">
                    <option value="">Not available</option>
                    <option v-for="(value, index) in recipes[0]" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>    
            <button type="submit" class="btn btn-primary" @click.prevent="createProduct(); $emit('refreshProduct')">Submit</button>            
        </div>
    </div>
</template>