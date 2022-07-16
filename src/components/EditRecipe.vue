<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

// mutate props directly
let props = defineProps([
    'companies',
    'products',
    'recipeForm',
    'details'
]);

function addIngredient(){
    props.details.push({
        ID: 3,
        qty_needed: 0,
    });
}

function popIngredient(){
    props.details.pop();
}

function uniqueCheck(arr){
    let cnt = {};
    for(var i = 0; i < arr.length; i++){
        if(cnt[arr[i].ID] == 1){
            return false;
        }        
        cnt[arr[i].ID] = 1;
    }
    return true;
}

function updateRecipe(){
    let fullRecipe = JSON.parse(JSON.stringify(props.recipeForm));
    fullRecipe['ingredients'] = JSON.parse(JSON.stringify(props.details));
    if(uniqueCheck(fullRecipe['ingredients'])){
        axios.put(`http://localhost:8000/recipes/${props.recipeForm.ID}`, fullRecipe)
        .then(function(response) {
            console.log(response.data);
            window.location.reload();
        })
        .catch(function (error) {
            console.log(error);
        }).then(function() {
        
        }); 
    } else {
        alert('There are non-unique ingredients');
    }
}

</script>

<template>
<div class="card m-2">
    <div class="card-body">
        <h5 class="card-title">Edit</h5>
        <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="recipeForm.company_id">
                    <option v-for="(value, index) in props.companies" :key="value.ID" :value="value.ID">
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
                <div v-for="(value, index) in details" class="mb-2">
                    <select class="form-select" v-model="details[index].ID">
                        <option v-for="(value2, index2) in props.products" :key="value2.ID" :value="value2.ID">
                            {{value2.name}} {{'(' + value2.uom_name + ')'}}
                        </option>
                    </select>
                    <input type="number" class="form-control" v-model="details[index].qty_needed">
                </div>
                <button class="btn btn-primary me-1" @click="addIngredient()">Add Ingredient</button>
                <button class="btn btn-danger" @click="popIngredient()">Remove Last Ingredient</button>
            </div>        
            <button type="submit" class="btn btn-primary" @click="updateRecipe();">Submit</button>
    </div>
</div>
</template>