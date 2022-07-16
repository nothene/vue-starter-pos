<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';
import axios from 'axios';

let props = defineProps([
    'notes',
    'details'
]);

</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="lead card-title">Recipe Detail</div>
            <ul>
                <li v-for="(value, index) in props.details">
                    {{value.name + ": " + value.qty_needed + " " + value.uom_name}}
                </li>
            </ul>
            <div>
                <label class="pt-2">Methods:</label>
                {{props.notes}}
            </div>
        </div>
    </div>
</template>