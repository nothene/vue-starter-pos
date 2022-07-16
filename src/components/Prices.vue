<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';

let props = defineProps([
    'priceList'
])

</script>

<template>
    <div class="my-3">
        <div>
            <label class="form-label">Price</label>
            <input type="number" class="form-control">
            <label class="form-label">Date & Time</label>
            <input type="date" class="form-control">     
            <input type="time">        
            <label class="form-label">UTC+0</label>
        </div>
        <button type="button" class="btn btn-primary mt-1">Publish price</button>
    </div>    
    <ul class="list-group" v-for="(value, index) in priceList">
        <li class="list-group-item">{{value}}</li>
    </ul>
</template>