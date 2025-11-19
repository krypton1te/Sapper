<template>
  <div>
    я поле
  </div>
  <div class="matrix">
    <div v-for="(rowData, rowIndex) in field" :key="'row-a-' + rowIndex" class="matrix-row">
      <matrix-cell v-for="cell in rowData" :key="cell.id" :cell="cell" @on-cell-open="onCellOpen"
        @on-cell-flag="onCellFlag" @on-cell-open-many="onCellOpenMany" />
    </div>
  </div>
</template>

<script setup>
import MatrixCell from './MatrixCell.vue'
import { ref, onMounted, defineProps, computed } from "vue";
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
  createField();
})

const field = ref([]);
const cellWithoutMinesCount = computed(() => props.height * props.width - props.mineCount);
const openCells = ref(0);

const createField = () => {
  openCells.value = 0;
  const newField = [];
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
        type: "A",
        isOpen: false,
        isFlag: false,
      };
      row.push(cell);
      mines.push(cell);
    }
    newField.push(row)
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
          const neighborCell = newField[neighborRow][neighborCol];
          if (neighborCell && !neighborCell.isMine) {
            neighborCell.nearMineCount++;
          }
        }
      }
    }

    mines.splice(mineIndex, 1);
  }

  field.value = newField;
}

const getRandomInt = (max) => {
  return Math.floor(Math.random() * max);
}

const onCellFlag = (cell) => {
  if (cell.isOpen) {
    return;
  }

  // Цикл: нет метки -> флаг -> вопрос -> нет метки
  if (!cell.isFlag && !cell.isQuestion) {
    cell.isFlag = true;
    cell.isQuestion = false;
  } else if (cell.isFlag) {
    cell.isFlag = false;
    cell.isQuestion = true;
  } else {
    cell.isQuestion = false;
  }
}

const onCellOpen = (cell) => {
  if(cell.isOpen || cell.isFlag){
    return;
  }
  cell.isOpen = true;
  cell.isQuestion = false;
  openCells.value = openCells.value + 1;
  if (cell.isMine) {
    alert('game over');
    return;
  }
  if (openCells.value == cellWithoutMinesCount.value) {
    alert('win');
    return;
  }
  if (cell.nearMineCount === 0) {
    for (let rowOffset = -1; rowOffset <= 1; rowOffset++) {
      for (let colOffset = -1; colOffset <= 1; colOffset++) {
        if (rowOffset === 0 && colOffset === 0) continue;
        const neighborRow = cell.row + rowOffset;
        const neighborCol = cell.col + colOffset;

        if (neighborRow >= 0 && neighborRow < props.height && neighborCol >= 0 && neighborCol < props.width) {
          const neighborCell = field.value[neighborRow][neighborCol];
          if (!neighborCell.isOpen) {
            onCellOpen(neighborCell);
          }
        }
      }
    }
  }
}

const onCellOpenMany = (cell) => {
  let nearFlags = 0;
  if (cell.nearMineCount > 0) {
    for (let rowOffset = -1; rowOffset <= 1; rowOffset++) {
      for (let colOffset = -1; colOffset <= 1; colOffset++) {
        if (rowOffset === 0 && colOffset === 0) continue;
        const neighborRow = cell.row + rowOffset;
        const neighborCol = cell.col + colOffset;

        if (neighborRow >= 0 && neighborRow < props.height && neighborCol >= 0 && neighborCol < props.width) {
          const neighborCell = field.value[neighborRow][neighborCol];
          if (neighborCell.isFlag) {
            nearFlags ++;
          }
        }
      }
    }
  }
  debugger
  if(cell.nearMineCount == nearFlags){
    for (let rowOffset = -1; rowOffset <= 1; rowOffset++) { // todo дублирование, можно обобщить
      for (let colOffset = -1; colOffset <= 1; colOffset++) {
        if (rowOffset === 0 && colOffset === 0) continue;
        const neighborRow = cell.row + rowOffset;
        const neighborCol = cell.col + colOffset;

        if (neighborRow >= 0 && neighborRow < props.height && neighborCol >= 0 && neighborCol < props.width) {
          const neighborCell = field.value[neighborRow][neighborCol];
          if (!neighborCell.isFlag) {
            onCellOpen(neighborCell);
          }
        }
      }
    }
  }
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
