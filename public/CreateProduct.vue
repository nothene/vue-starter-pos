<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let props = defineProps([
    'companies'
]);

let recipes = reactive([]);

watchEffect(async () => { 
    axios.get('http://localhost:8000/recipes').then(
        function(response){
            recipes.push(response.data);
            console.log(response.data);
        }
    ).catch(function(error){
        console.log(error);
        alert(error);
    })
});

let productForm = reactive({
    company_id: 1,
    code: '',
    name: '',
    color: '',
    is_raw_material: true,
    is_active: true,
    uom_name: 'pcs',
    recipe_id: 1,
})

function createProduct(){
    console.log(productForm);
    axios.post('http://localhost:8000/products',
        productForm
    ).then(
        function(response){
            console.log(response);
            alert(response);
            productForm = reactive({
                company_id: 1,
                code: '',
                name: '',
                color: '',
                is_raw_material: false,
                is_active: true,
                uom_name: 'pcs',
                recipe_id: null,
            })            
        }
    ).catch(function(error){
        alert(error);
    })
}

</script>

<template>
    <div class="card">
        <div class="card-body">
            <form method="post" action="http://localhost:8000/products">
                <div class="card-title lead">Create New Product</div>
                <div class="m-1">
                    <div class="mb-3">
                        <label class="form-label">Company</label>
                            <select class="form-select" v-model="productForm.company_id">
                                <option v-for="(value, index) in companies" :key="value.ID" :value="value.ID">
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
                            <option selected value=true>Yes</option>
                            <option value=false>No</option>
                        </select>
                    </div>                         
                    <div class="mb-3">
                        <label class="form-label">Is Active</label>
                        <select class="form-select" v-model="productForm.is_active">
                        <option selected value=true>Yes</option>
                        <option value=false>No</option>
                        </select>
                    </div>    
                    <div class="mb-3">
                        <label class="form-label">Unit of Measurement Name</label>
                        <input type="text" class="form-control" v-model="productForm.uom_name">
                    </div>       
                    <div class="mb-3">
                        <label class="form-label">Recipe</label>
                            <select class="form-select" v-model="productForm.recipe_id">
                                <option v-for="(value, index) in recipes[0]" :key="value.ID" :value="value.ID">
                                    {{value.name}}
                                </option>
                            </select>
                    </div>  
                    <button type="submit" @click.prevent="createProduct" class="btn btn-primary">Submit</button>                                       
                </div>
            </form>
        </div>
    </div> 
</template>

<style>

</style>