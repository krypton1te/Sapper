<template>
  <div>
    я поле
  </div>
  <div class="matrix">
    <div v-for="(rowData, rowIndex) in matrix" :key="'row-a-' + rowIndex" class="matrix-row">
      <matrix-cell v-for="cell in rowData" :key="cell.id" :cell="cell" @cell-click="cellClick"/>
    </div>
  </div>
</template>

<script setup>
import MatrixCell from './MatrixCell.vue'
import { ref,onMounted,defineProps  } from "vue";
const props = defineProps({
  width: {
    type: Number,
    default: 0
  },
  height: {
    type: Number,
    default: 0
  },
  mineCount: {
    type: Number,
    default: 0
  }
});

onMounted(() => {
  createMatrix();
})

const matrix = ref([]);

const createMatrix =() => {
  const newMatrix = [];
  const mines = [];
  for (let i = 0; i < props.height; i++) {
    const row = [];
    for (let j = 0; j < props.width; j++) {
      const cell = {
        row: i,
        col: j,
        id: `cell-${i}-${j}`,
        isMine: false,
        nearMineCount: 0,
        type: "A"
      };
      row.push(cell);
      mines.push(cell);
    }
    newMatrix.push(row)
  }
  for (let i = 0; i < props.mineCount; i++) {
    let mineIndex = getRandomInt(mines.length);

    mines[mineIndex].isMine = true;

    for (let rowOffset = -1; rowOffset <= 1; rowOffset++) {
      for (let colOffset = -1; colOffset <= 1; colOffset++) {
        if (rowOffset === 0 && colOffset === 0) continue;
        const neighborRow = mines[mineIndex].row + rowOffset;
        const neighborCol = mines[mineIndex].col + colOffset;

        if (neighborRow >= 0 && neighborRow < props.height && neighborCol >= 0 && neighborCol < props.width) {
          const neighborCell = newMatrix[neighborRow][neighborCol];
          if (neighborCell && !neighborCell.isMine) {
            neighborCell.nearMineCount++;
          }
        }
      }
    }

    mines.splice(mineIndex, 1);
  }

  matrix.value = newMatrix;
}

const getRandomInt = (max)=> {
  return Math.floor(Math.random() * max);
}

const cellClick = (cell)=> {
  console.log(cell);
}
</script>

<style scoped lang="scss">
.app {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.matrix {
  display: inline-block;
  border: 2px solid #ddd;
  border-radius: 8px;
  overflow: hidden;

  &-container {
    margin-bottom: 40px;
  }

  &-row {
    display: flex;
  }
}
</style>
