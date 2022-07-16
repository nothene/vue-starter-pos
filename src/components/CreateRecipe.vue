<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let recipeDetail = {
    ID: 1,
    qty_needed: 0,
}

let recipeForm = reactive({
    company_id: 1,
    code: '',
    name: '',
    notes: '',
    ingredients: [{
        ID: 1,
        qty_needed: 0,
    }]
});

let recipes = reactive([]);

let props = defineProps([
    'companies', 'products'
])

watchEffect(async () => {
    await axios.get()
        .then(function(response) {
            recipes.push(response.data);
            console.log(props.products);
        })
        .catch(function (error) {
            console.log(error);
        });      
    }   
);

function createRecipe(){
    console.log(recipeForm);
    axios.post('http://localhost:8000/recipes', recipeForm)
    .then(function(response) {
        console.log(response.data);
        alert('Recipe created successfully');
        Object.assign(recipeForm, {
            company_id: 1,
            code: '',
            name: '',
            notes: '',
            ingredients: [{
                    ID: 1,
                    qty_needed: 0,
               }]
        });
        window.location.reload();
    })
    .catch(function (error) {
        console.log(recipeForm);
        console.log(error);
        alert(error);
    }).then(function() {
    
    });
}

function print(){
    
}

function addIngredient(){
    recipeForm.ingredients.push({
        ID: 1,
        qty_needed: 0,
    });
}

function popIngredient(){
    recipeForm.ingredients.pop();
}

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="card-title">Create Recipe</div>
                <div class="mb-3">
                    <label class="form-label">Company</label>
                    <select class="form-select" v-model="recipeForm.company_id">
                        <option v-for="(value, index) in companies" :key="value.ID" :value="value.ID">
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
                    <div v-for="(value, index) in recipeForm.ingredients">
                        <select class="form-select" v-model="recipeForm.ingredients[index].ID">
                            <option v-for="(value2, index2) in props.products" :key="value2.ID" :value="value2.ID">
                                {{value2.name}} {{'(' + value2.uom_name + ')'}}
                            </option>
                        </select>
                        <input type="number" class="form-control" v-model="recipeForm.ingredients[index].qty_needed">
                        <span>{{recipeForm.ingredients[index]}}</span>
                    </div>
                    <button class="btn btn-primary" @click="addIngredient()">Add Ingredient</button>
                    <button class="btn btn-danger" @click="popIngredient()">Remove Last Ingredient</button>
                </div>        
                <button type="submit" class="btn btn-primary" @click="createRecipe(); $emit('refreshRecipe')">Submit</button>
        </div>
    </div>
</template>