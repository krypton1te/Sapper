<script setup>
import MatrixCell from './MatrixCell.vue'
</script>

<template>
  <div>
    я поле
  </div>
      <div class="matrix">
        <div 
          v-for="(rowData, rowIndex) in matrix" 
          :key="'row-a-' + rowIndex"
          class="matrix-row"
        >
      
          <MatrixCell 
          v-for="cell in rowData" 
            :key="cell.id"
            :row="cell.row"
            :col="cell.col"
            :value="cell.value"
            matrix-type="A"
          /> 
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
    }
  }, 
   data() {
    const newMatrix = []
    for (let i = 0; i < this.height; i++) {
        const row = []
        for (let j = 0; j < this.width; j++) {
          row.push({
            row: i,
            col: j,
            value: i * this.height + j + 1,
            id: `cell-${i}-${j}`,
          })
        }
        newMatrix.push(row)
    }
  
     return {
        matrix: newMatrix
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