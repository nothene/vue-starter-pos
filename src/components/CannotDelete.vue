<script setup>
import { ref, reactive } from 'vue';

</script>
<template>
    <div class="modal fade" id="staticBackdrop" data-bs-backdrop="static" data-bs-keyboard="false" tabindex="-1" aria-labelledby="staticBackdropLabel" aria-hidden="true">
    <div class="modal-dialog">
        <div class="modal-content">
        <div class="modal-header">
            <h5 class="modal-title" id="staticBackdropLabel">Delete Fail</h5>
            <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
        </div>
        <div class="modal-body">
            This row cannot be deleted, most likely because there is another row depending on it. Try deleting bottom rows to test the delete functionality. Press OK to continue... 
        </div>
        <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">OK</button>
        </div>
        </div>
    </div>
    </div> 
</template>