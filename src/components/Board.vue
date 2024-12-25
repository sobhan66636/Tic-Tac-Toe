<!-- src/components/Board.vue -->
<template>
    <div class="board d-grid gap-2" style="grid-template-columns: repeat(3, 100px); grid-template-rows: repeat(3, 100px);">
      <Square v-for="(square, index) in board" :key="index" :value="square" :index="index" :onClick="makeMove" />
    </div>
  </template>
  
  <script setup>
  import Square from './Square.vue';
  
  const props = defineProps({
    board: Array,
    makeMove: Function,
  });
  </script>
  
  <style scoped>
  .board {
    margin: 20px auto;
  }
  </style>
  