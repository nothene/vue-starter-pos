<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';

let props = defineProps([
    'companiesMap',
    'products',
]);

let sellForm = reactive({
    company_id: 1,
    transaction_date: '',
    customer_name: '',
    is_cancelled: false,
    cancelled_at: '',
    is_printed: false,
    printed_at: '',
    details: [],
    notes: ''
});


</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="card-title lead">Create Sell</div>
            <div class="mb-3">
                <label class="form-label">Company</label>
                <select class="form-select" v-model="sellForm.company_id">
                    <option v-for="(value, index) in companiesMap" :key="index" :value="index">
                        {{value}}
                    </option>
                </select>
            </div>
        </div>
    </div>        
</template>