<script setup>
import SupportIcon from './icons/IconSupport.vue'
</script>
<template>
  <div class="matrix-cell" :class="[matrixType, { 'even': isEven }]" @click="handleClick"
    @contextmenu.prevent="handleRightClick" @mouseenter="isHovered = true"
    @mouseleave="isHovered = false">
    <div class="value">
      <span v-if="isOpen" :class="[{ 'mine-cell': isMine }]">
        {{ nearMineCount }}
      </span>
      <span v-if="isFlag">
        <SupportIcon />
      </span>
    </div>
    <div class="coordinates" v-if="isHovered">
      [{{ row }},{{ col }}]
    </div>
  </div>
</template>

<script>
export default {
  name: 'MatrixCell',
  props: {
    row: {
      type: Number,
      required: true
    },
    col: {
      type: Number,
      required: true
    },
    matrixType: {
      type: String,
      default: 'A'
    },
    isMine: {
      type: Boolean,
      required: true,
    },
    nearMineCount: {
      type: Number,
      required: true,
    }
  },
  data() {
    return {
      isHovered: false,
      isOpen: false,
      isFlag: false,
    }
  },
  computed: {
    isEven() {
      return this.nearMineCount % 2 === 0
    }
  },
  methods: {
    handleRightClick(){
      this.isFlag = true;
    },
    handleClick(event) {
    const clickType = event.button === 2 ? 'right' : 'left';
    if (clickType) {
        this.isOpen = true;
    }//else {
    //    this.isFlag = true;
     // }
      //event.preventDefault();

      this.$emit('cell-click', {
        row: this.row,
        col: this.col,
        nearMineCount: this.nearMineCount,
        matrixType: this.matrixType
      })

      console.log(`Клик по матрице ${this.matrixType}: [${this.row}, ${this.col}] = ${this.nearMineCount}`)
    }
  }
}
</script>

<style scoped>
.matrix-cell {
  width: 50px;
  height: 50px;
  border: 1px solid #bdc3c7;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  transition: all 0.2s ease;
  position: relative;
  font-size: 14px;
}

.matrix-cell .mine-cell {
  background: darkred !important;
}

.matrix-cell:hover {
  transform: scale(1.1);
  z-index: 10;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
}

/* Стили для матрицы A */
.matrix-cell.A {
  background: #ecf0f1;
}

.matrix-cell.A:hover {
  background: #d5dbdb;
}

/* Стили для матрицы B */
.matrix-cell.B {
  background: #e8f6f3;
}

.matrix-cell.B:hover {
  background: #d0ece7;
}

/* Чётные/нечётные */
.matrix-cell.even {
  font-weight: bold;
}

.matrix-cell:not(.even) {
  color: #2c3e50;
}

.value {
  font-size: 16px;
  font-weight: bold;
}

.coordinates {
  position: absolute;
  bottom: 2px;
  font-size: 9px;
  color: #7f8c8d;
}
</style>