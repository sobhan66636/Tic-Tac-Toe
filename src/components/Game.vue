<script setup>
import { ref, watch } from 'vue'
import Board from './Board.vue'
import Scoreboard from './Scoreboard.vue'
import Modal from './Modal.vue'

// Props
const props = defineProps({
  isAI: Boolean // Determines if the opponent is an AI
})

const emit = defineEmits(['reset'])

// Game state variables
const board = ref(Array(9).fill(null))
const currentPlayer = ref('X')
const winner = ref(null)
const isDraw = ref(false)
const isModalVisible = ref(false)
const modalMessage = ref('')
const xWins = ref(parseInt(localStorage.getItem('xWins')) || 0)
const oWins = ref(parseInt(localStorage.getItem('oWins')) || 0)

// History for the moves
const gameHistory = ref([Array(9).fill(null)])
let historyIndex = ref(0)

// Winning logic
const checkWinner = () => {
  const winPatterns = [
    [0, 1, 2], [3, 4, 5], [6, 7, 8], // Rows
    [0, 3, 6], [1, 4, 7], [2, 5, 8], // Columns
    [0, 4, 8], [2, 4, 6]             // Diagonals
  ]

  for (let pattern of winPatterns) {
    const [a, b, c] = pattern
    if (board.value[a] && board.value[a] === board.value[b] && board.value[a] === board.value[c]) {
      winner.value = board.value[a]
      return true
    }
  }
  if (!board.value.includes(null)) {
    isDraw.value = true
  }
  return false
}

// Handle player's move
const makeMove = (index) => {
  // Block the player's move if it's the AI's turn
  if (board.value[index] || isDraw.value || winner.value || (props.isAI && currentPlayer.value === 'O')) {
    return
  }
  
  board.value[index] = currentPlayer.value
  gameHistory.value.push([...board.value])  // Store the current state in history
  historyIndex.value++

  if (checkWinner()) {
    updateScore(winner.value)
    showModal(`${winner.value === 'X' ? (props.isAI ? 'You' : 'Player X') : (props.isAI ? 'Bot' : 'Player O')} wins!`)
    return
  }
  if (isDraw.value) {
    showModal("It's a draw!")
    return
  }
  currentPlayer.value = currentPlayer.value === 'X' ? 'O' : 'X'

  // Trigger AI's move if in AI mode and it's the AI's turn
  if (props.isAI && currentPlayer.value === 'O') {
    setTimeout(aiMove, 500) // Add delay for AI move
  }
}
// AI logic for making a move
const aiMove = () => {
  const findBestMove = () => {
    const winPatterns = [
      [0, 1, 2], [3, 4, 5], [6, 7, 8], // Rows
      [0, 3, 6], [1, 4, 7], [2, 5, 8], // Columns
      [0, 4, 8], [2, 4, 6]             // Diagonals
    ]
    for (let pattern of winPatterns) {
      const [a, b, c] = pattern
      const line = [board.value[a], board.value[b], board.value[c]]
      // Check for winning move
      if (line.filter(v => v === 'O').length === 2 && line.includes(null)) {
        return pattern[line.indexOf(null)]
      }
      // Check for blocking move
      if (line.filter(v => v === 'X').length === 2 && line.includes(null)) {
        return pattern[line.indexOf(null)]
      }
    }
    return null
  }

  const bestMove = findBestMove()
  if (bestMove !== null) {
    board.value[bestMove] = 'O'
  } else {
    // Pick a random available square
    const availableMoves = board.value.map((square, index) => square === null ? index : -1).filter(index => index !== -1)
    const randomMove = availableMoves[Math.floor(Math.random() * availableMoves.length)]
    board.value[randomMove] = 'O'
  }
  gameHistory.value.push([...board.value])  // Store the current state in history
  historyIndex.value++

  if (checkWinner()) {
    updateScore(winner.value)
    showModal(`${winner.value === 'X' ? (props.isAI ? 'You' : 'Player X') : (props.isAI ? 'Bot' : 'Player O')} wins!`)
    return
  }
  if (isDraw.value) {
    showModal("It's a draw!")
    return
  }
  currentPlayer.value = 'X' // Return turn to player
}

// Reset game state
const resetGame = () => {
  board.value = Array(9).fill(null)
  currentPlayer.value = 'X'
  winner.value = null
  isDraw.value = false
  isModalVisible.value = false
  gameHistory.value = [Array(9).fill(null)] // Reset game history
  historyIndex.value = 0
}

// Update scoreboard and win history
const updateScore = (winner) => {
  if (winner === 'X') {
    xWins.value++
  } else if (winner === 'O') {
    oWins.value++
  }
  localStorage.setItem('xWins', xWins.value)
  localStorage.setItem('oWins', oWins.value)
}

// Show modal for game result
const showModal = (message) => {
  modalMessage.value = message
  isModalVisible.value = true
}

// Close modal
const closeModal = () => {
  isModalVisible.value = false
}

// Back to menu
const backToMenu = () => {
  emit('reset')
}
// Replay game move
const replayMove = () => {
  if (historyIndex.value < gameHistory.value.length - 1) {
    historyIndex.value++
    board.value = [...gameHistory.value[historyIndex.value]]
  }
}

// Rewind game move
const rewindMove = () => {
  if (historyIndex.value > 0) {
    historyIndex.value--
    board.value = [...gameHistory.value[historyIndex.value]]
  }
}

// Clear game history and reset scoreboard
const clearHistory = () => {
  xWins.value = 0
  oWins.value = 0
  localStorage.removeItem('xWins')
  localStorage.removeItem('oWins')
}
</script>

<template>
  <div class="container text-center">
    <Scoreboard :xWins="xWins" :oWins="oWins" />

    <!-- Turn or Result Display -->
    <div v-if="!winner && !isDraw" class="turn-indicator my-3">
      <h3>{{ currentPlayer === 'X' ? 'X Turn' : 'O Turn' }}</h3>
    </div>
    <div v-if="winner || isDraw" class="result-display my-3">
      <h3>{{ winner ? (winner === 'X' ? 'X win!' : 'O wins!') : "It's a draw!" }}</h3>
    </div>

    <!-- Game History Controls -->
    <div v-if="gameHistory.length > 1 && (winner || isDraw)" class="history-controls my-3">
      <button @click="rewindMove" class="btn btn-secondary mr-2">Rewind</button>
      <button @click="replayMove" class="btn btn-secondary">Replay</button>
    </div>

    <div class="d-flex justify-content-center">
      <Board :board="board" :makeMove="makeMove" />
    </div>

    <!-- Button group for better UI -->
    <div class="btn-group mt-3" role="group" aria-label="Game actions">
      <button @click="resetGame" class="btn btn-primary">New Game</button>
      <button @click="clearHistory" class="btn btn-warning">Clear History</button>
      <button @click="backToMenu" class="btn btn-secondary">Back to Menu</button>
    </div>

    <Modal :isVisible="isModalVisible" :message="modalMessage" @close="closeModal" />
  </div>
</template>

<style scoped>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.turn-indicator {
  font-size: 1.5rem;
  color: #333;
}

.my-2 {
  margin-top: 0.5rem; /* Reduced space between scoreboard and board */
}

.btn-group {
  display: flex;
  gap: 0.5rem;
  justify-content: center;
}

h1 {
  font-size: 2.5rem;
  color: #333;
}

.my-4 {
  margin-top: 1rem;
  margin-bottom: 1rem;
}
</style>
