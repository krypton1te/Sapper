<template>
  <div class="matrix-cell"
       :class="[props.cell.type, { 'even': isEven }, { 'mine-cell':  isOpen && isMine}]"
       @click="handleClick"
       @contextmenu.prevent="handleRightClick"
       @mouseenter="isHovered = true"
       @mouseleave="isHovered = false">
    <div class="value" >
      <span v-if="isOpen && !isMine">
        {{ props.cell.nearMineCount }}
      </span>
      <span v-if="isFlag">
        <SupportIcon />
      </span>
    </div>
    <div class="coordinates" v-if="isHovered">
      [{{ props.cell.row }},{{ props.cell.col }}]
    </div>
  </div>
</template>
<script setup>
import SupportIcon from './icons/IconSupport.vue'
import { ref, defineProps, defineEmits, computed } from "vue";
const props = defineProps({
  cell:Object
});
const isHovered = ref(false);
const isOpen = ref(false);
const isFlag = ref(false);

const emit = defineEmits(['cell-click'])

const isEven = computed(() => Boolean(props.cell.nearMineCount % 2 === 0));
const isMine = computed(() => Boolean(props.cell.isMine));

const handleClick = (event) => {
  const clickType = event.button === 2 ? 'right' : 'left';
  if (clickType) {
    isOpen.value = true;
  }
  emit('cell-click', props.cell)
}

const handleRightClick = ()=> {
  isFlag.value = true;
}
</script>

<style scoped lang="scss">
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
  &.mine-cell {
    background: darkred !important;
  }
  &:hover {
    transform: scale(1.1);
    z-index: 10;
    box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
  }
  /* Стили для матрицы A */
  &.A{
    background: #ecf0f1;
    &:hover{
      background: #d5dbdb;
    }
  }
  &.B{
    background: #e8f6f3;
    &:hover{
      background: #d0ece7;
    }
  }

  &.even {
    font-weight: bold;
  }
  :not(.even) {
    color: #2c3e50;
  }
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
