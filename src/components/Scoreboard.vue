<!-- src/components/Scoreboard.vue -->
<template>
  <div>
    <p class="h5">X Wins: {{ xWins }}</p>
    <p class="h5">O Wins: {{ oWins }}</p>
  </div>
</template>

<script setup>
const props = defineProps({
  xWins: Number,
  oWins: Number,
});
</script>

<style scoped>
</style>
