<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let recipeForm = reactive({
    company_id: 1,
    code: '',
    name: '',
    notes: '',
    ingredients: []
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

function createRecipe(){
    recipeForm.ingredients
    axios.post(`http://localhost:8000/recipes`, recipeForm)
    .then(function(response) {
        console.log(response.data);
        alert('Product created successfully');
        Object.assign(recipeForm, {
            company_id: 1,
            code: '',
            name: '',
            notes: '',
            ingredients: []
        });
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
            <div class="card-title">Create Recipe</div>
            <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="recipeForm.company_id">
                    <option v-for="(value, index) in prop.companies" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
            </div>
            <div class="mb-3">
                <label class="form-label">Code</label>
                <input type="text" class="form-control" v-model="recipeForm.code">
            </div>            
            <div class="mb-3">
                <label class="form-label">Name</label>
                <input type="text" class="form-control" v-model="recipeForm.name">
            </div>
            <div class="mb-3">
                <label class="form-label">Notes</label>
                <input type="text" class="form-control" v-model="recipeForm.notes">
            </div>
            <div class="mb-3">
                <label class="form-label">Add Ingredients</label>
                <select class="form-select" v-model="recipeForm.company_id">
                    <option v-for="(value, index) in prop.companies" :key="value.ID" :value="value.ID">
                        {{value.name}}
                    </option>
                </select>
                <button class="btn btn-primary">Add Ingredient</button>
            </div>            
            <button type="submit" class="btn btn-primary" @click="createRecipe(); $emit('refreshRecipe')">Submit</button>            
        </div>
    </div>
</template>