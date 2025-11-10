<script setup>
import MatrixCell from './MatrixCell.vue'
</script>

<template>
  <div>
    я поле
  </div>
  <div class="matrix">
    <div v-for="(rowData, rowIndex) in matrix" :key="'row-a-' + rowIndex" class="matrix-row">
      <MatrixCell v-for="cell in rowData" :key="cell.id" :row="cell.row" :col="cell.col" :isMine="cell.isMine"
        :nearMineCount="cell.nearMineCount" matrix-type="A" />
    </div>
  </div>
</template>

<script>
export default {
  name: 'Field',
  props: {
    width: {
      type: Number,
      required: true
    },
    height: {
      type: Number,
      required: true
    },
    mineCount: {
      type: Number,
      required: true
    }
  },
  data() {
    return {
      matrix: [],
    }
  },
  mounted() {
    this.createMatrix();
  },
  methods: {
    createMatrix() {
      const newMatrix = []
      let mines = [];
      for (let i = 0; i < this.height; i++) {
        const row = []
        for (let j = 0; j < this.width; j++) {
          let cell = {
            row: i,
            col: j,
            id: `cell-${i}-${j}`,
            isMine: false,
            nearMineCount: 0,
          };
          row.push(cell)
          mines.push(cell)
        }
        newMatrix.push(row)
      }
      for (let i = 0; i < this.mineCount; i++) {
        let mineIndex = getRandomInt(mines.length);
        console.log(mines[mineIndex].row + " " + mines[mineIndex].col);
        mines[mineIndex].isMine = true;

        for (let rowOffset = -1; rowOffset <= 1; rowOffset++) {
          for (let colOffset = -1; colOffset <= 1; colOffset++) {
            if (rowOffset === 0 && colOffset === 0) continue;
            const neighborRow = mines[mineIndex].row + rowOffset;
            const neighborCol = mines[mineIndex].col + colOffset;

            if (neighborRow >= 0 && neighborRow < this.height &&
              neighborCol >= 0 && neighborCol < this.width) {
              const neighborCell = newMatrix[neighborRow][neighborCol];
              if (neighborCell && !neighborCell.isMine) {
                neighborCell.nearMineCount++;
              }
            }
          }
        }

        mines.splice(mineIndex, 1);
      }

      function getRandomInt(max) {
        return Math.floor(Math.random() * max);
      }
      this.matrix = newMatrix;
    },
    cellClick(value) {
      const cell = this.matrix[value.row][value.col];
      console.log(`Клик по матрице:[${cell.row}, ${cell.col}] = ${cell.nearMineCount}`)
    }
  }

}

</script>


<style scoped>
.app {
  padding: 20px;
  font-family: Arial, sans-serif;
}

.matrix-container {
  margin-bottom: 40px;
}

.matrix {
  display: inline-block;
  border: 2px solid #ddd;
  border-radius: 8px;
  overflow: hidden;
}

.matrix-row {
  display: flex;
}
</style>