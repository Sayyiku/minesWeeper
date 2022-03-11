<script setup lang="ts" >

interface BlockState {
  x: number
  y: number
  revealed?: boolean
  mine?: boolean
  flagged?: boolean
  adjacentMines?: number

}

const Width = 10
const Height = 10
const state = reactive(
  Array.from({ length: Height }, (_, y) =>
    Array.from({ length: Width },
      (_, x): BlockState => ({
        x, y,
      }),
    ),
  ),
)

function generateMines() {
  for (const row of state) {
    for (const block of row)
      block.mine = Math.random() < 0.3
  }
}

function updateNumers() {
  for (const row of state) {
    for (const block of row) {
      if (block.mine) continue
      block.adjacentMines = 0
      for (const [dx, dy] of [
        [-1, -1], [-1, 0], [-1, 1],
        [0, -1], [0, 1],
        [1, -1], [1, 0], [1, 1],
      ]) {
        const x = block.x + dx
        const y = block.y + dy
        if (x < 0 || x >= Width || y < 0 || y >= Height) continue
        if (state[y][x].mine) block.adjacentMines++
      }
    }
  }
}

function onclick(x: number, y: number) {
  // eslint-disable-next-line no-console,no-template-curly-in-string
  console.log('Click at ${x}, ${y}')
}

generateMines()
updateNumers()

</script>

<template>
  <div>
    Minesweeper
    <div p5>
      <div
        v-for="row,y in state"
        :key="y"
      >
        <button
          v-for="item,x in row"
          :key="x"
          w-10
          h-10
          hover:bg-gray
          border
          :class="item.mine ? 'text-red' : 'text-gray'"
          @click="onclick(x,y)"
        >
          <div v-if="item.mine">
            💣
          </div>
          <div v-else>
            {{ item.adjacentMines }}
          </div>
        </button>
      </div>
    </div>
  </div>
</template>
