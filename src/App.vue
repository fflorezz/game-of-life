<script setup>
import { ref } from 'vue'

const isGameRunning = ref(false)
const intervalId = ref(null)

const makeGrid = (rows, cols) => {
  const grid = []
  for (let i = 0; i < rows; i++) {
    const row = []
    for (let j = 0; j < cols; j++) {
      row.push(Math.round(Math.random(2)))
    }
    grid.push(row)
  }
  return grid
}

const grid = ref(makeGrid(20, 20))

const getNeighbors = (grid, row, col) => {
  const neighbors = []
  const rows = grid.length
  const cols = grid[0].length
  for (let i = -1; i <= 1; i++) {
    const neighborRow = row + i
    if (neighborRow < 0 || neighborRow >= rows) continue
    for (let j = -1; j <= 1; j++) {
      const neighborCol = col + j
      if (neighborCol < 0 || neighborCol >= cols) continue
      if (i === 0 && j === 0) continue
      neighbors.push(grid[neighborRow][neighborCol])
    }
  }
  return neighbors
}

const isCellAlive = (cell, neighbors) => {
  const aliveNeighbors = neighbors.filter(n => n === 1).length
  if (cell === 1) {
    if (aliveNeighbors < 2) return 0
    if (aliveNeighbors === 2 || aliveNeighbors === 3) return 1
    if (aliveNeighbors > 3) return 0
  } else {
    if (aliveNeighbors === 3) return 1
  }
  return cell
}

const nextGeneration = grid => {
  const nextGrid = []
  for (let i = 0; i < grid.length; i++) {
    const row = []
    for (let j = 0; j < grid[0].length; j++) {
      const cell = grid[i][j]
      const neighbors = getNeighbors(grid, i, j)
      const nextCell = isCellAlive(cell, neighbors)
      row.push(nextCell)
    }
    nextGrid.push(row)
  }
  return nextGrid
}

const creatNextGeneration = () => {
  const nextGrid = nextGeneration(grid.value)
  grid.value = nextGrid
}

const start = () => {
  intervalId.value = setInterval(creatNextGeneration, 500)
}

const pause = () => {
  clearInterval(intervalId.value)
}

const reset = () => {
  grid.value = makeGrid(20, 20)
}

const toggleGame = () => {
  if (isGameRunning.value) {
    pause()
  } else {
    start()
  }
  isGameRunning.value = !isGameRunning.value
}
</script>

<template>
  <button @click="toggleGame">{{ isGameRunning ? 'Pause' : 'Start' }}</button>
  <button @click="reset">Reset</button>
  <div>
    <div class="row" v-for="row in grid" :key="row">
      <div
        class="cell"
        :class="{ live: cell == 1 }"
        v-for="cell in row"
        :key="cell"
      ></div>
    </div>
  </div>
</template>

<style scoped>
.row {
  display: flex;
}
.cell {
  width: 20px;
  height: 20px;
  border: 1px solid black;
}
.live {
  background-color: black;
}
button {
  margin-top: 20px;
  margin-bottom: 20px;
  background-color: black;
  color: white;
  padding-left: 50px;
  padding-right: 50px;
  margin-left: 10px;
}
</style>
