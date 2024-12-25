<!-- src/components/Square.vue -->
<template>
    <button class="square btn btn-outline-secondary" @click="handleClick">
      {{ value }}
    </button>
  </template>
  
  <script setup>
  const props = defineProps({
    value: String,
    index: Number,
    onClick: Function,
  });
  
  const handleClick = () => {
    props.onClick(props.index);
  };
  </script>
  
  <style scoped>
  .square {
    width: 100px;
    height: 100px;
    font-size: 2em;
    text-align: center;
    background-color: #f4f4f4;
    border: 1px solid #ccc;
    cursor: pointer;
  }
  
  .square:hover {
    background-color: #e0e0e0;
  }
  </style>
  