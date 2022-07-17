<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

// mutate props directly
let props = defineProps([
    'companies',
    'productForm',
    'recipes'
]);

function updateProduct(){
    let curProductForm = JSON.parse(JSON.stringify(props.productForm));
    axios.put(`http://localhost:8000/products/${curProductForm.ID}`, curProductForm)
    .then(function(response) {
        alert(response);            
        window.location.reload();
    })
    .catch(function (error) {
        console.log(error);
    }).then(function() {
    
    }); 
}

</script>

<template>
<div class="card m-2">
    <div class="card-body">
        <h5 class="card-title">Edit</h5>
        <div class="mb-3">
            <label class="form-label">Company</label>
            <select class="form-select" v-model="productForm.company_id">
                <option v-for="(value, index) in props.companies" :key="value.ID" :value="value.ID">
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
                    <option v-for="(value, index) in props.recipes" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>    
            <button type="submit" class="btn btn-primary" @click="updateProduct();">Submit</button>
    </div>
</div>
</template>