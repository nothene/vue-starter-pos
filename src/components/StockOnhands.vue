<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import CardLayout from './CardLayout.vue';

let props = defineProps([
    'stockOnhands',
    'companiesMap'
])

</script>

<template>
    <CardLayout>
        <template #header>
            <span>Stocks Onhand</span> 
        </template>
        <template #body>
            <ul v-for="(value, index) in stockOnhands">
                <li>
                    <span>{{companiesMap[value.company_id]}} </span>
                    {{": "}}
                    <span>{{value.qty}} </span>
                </li>
            </ul>
            
        </template>
    </CardLayout>
</template>