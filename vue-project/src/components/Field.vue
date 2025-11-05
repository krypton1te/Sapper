<script setup>
import MatrixCell from './MatrixCell.vue'
</script>

<template>
  <div>
    я поле
  </div>
      <div class="matrix">
        <div 
          v-for="row in 10" 
          :key="'row-a-' + row"
          class="matrix-row"
        >
          <MatrixCell 
            v-for="col in 10" 
            :key="`cell-a-${row}-${col}`"
            :row="row"
            :col="col"
            :value="(row - 1) * 10 + col"
            matrix-type="A"
          />
        </div>
      </div>
</template>

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