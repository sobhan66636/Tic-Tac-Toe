<template>
  <div id="home-view" class="container text-center">

    <!-- Game Menu -->
    <div v-if="!gameStarted" class="menu">
      <h1>Tic-Tac-Toe Game</h1>
      <h2 class="mb-3">Choose Game Mode</h2>
      <div class="d-flex justify-content-center gap-3">
        <button @click="startGame(false)" class="btn btn-success btn-lg w-50">
          Player vs Player
        </button>
        <button @click="startGame(true)" class="btn btn-warning btn-lg w-50">
          Player vs AI
        </button>
      </div>
    </div>

    <!-- Game Component -->
    <Game v-if="gameStarted" :isAI="playWithAI" @reset="resetGame" />
  </div>
</template>

<script setup>
import { ref } from 'vue'
import Game from '../components/Game.vue'

// States
const gameStarted = ref(false)
const playWithAI = ref(false)

// Start the game with the selected mode
const startGame = (isAI) => {
  playWithAI.value = isAI
  gameStarted.value = true
}

// Reset game to return to the menu
const resetGame = () => {
  gameStarted.value = false
  playWithAI.value = false
}
</script>

<style scoped>
#home-view {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

h1 {
  font-size: 2.5rem;
  color: #333;
  margin: 2rem;
}

.menu {
  margin-top: 2rem;
}

.menu h2 {
  font-size: 1.8rem;
  color: #555;
}

button {
  font-size: 1.25rem;
  padding: 0.75rem 1.5rem;
}
</style>
