<script setup>
</script>

<template>
    <div class="card m-2">
        <div class="card-body">
            <div class="lead card-title">
                <slot name="header"></slot>
            </div>
            <slot name="body"></slot>                    
        </div>
    </div>         
</template>