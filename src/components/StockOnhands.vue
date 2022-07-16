<script setup>
import { ref, reactive, computed, watch, onMounted, watchEffect } from 'vue';

let props = defineProps([
    'stockOnhands'
])

</script>

<template>
    <div>
        {{stockOnhands}}
    </div>    
</template>