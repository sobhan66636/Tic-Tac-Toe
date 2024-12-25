<!-- src/components/Modal.vue -->
<template>
    <div v-if="isVisible" class="modal fade show" tabindex="-1" aria-labelledby="modalLabel" style="display: block;">
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title" id="modalLabel">Game Over</h5>
            <button type="button" class="btn-close" @click="onClose"></button>
          </div>
          <div class="modal-body">
            <p>{{ message }}</p>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" @click="onClose">Close</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script setup>
  const props = defineProps({
    isVisible: Boolean,
    message: String,
  });
  
  const emit = defineEmits(['close']);
  
  const onClose = () => {
    emit('close');
  };
  </script>
  
  <style scoped>
  </style>
  