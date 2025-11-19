<template>
  <div class="cell" :class="cellClasses" @click="handleClick" @contextmenu.prevent="handleRightClick"
    @mouseenter="isHovered = true" @mouseleave="isHovered = false" @mousedown="handleMouseDown"
    @mouseup="handleMouseUp">

    <div class="cell-content">
      <span v-if="isOpen && isMine" class="mine">💣</span>
      <span v-if="isOpen && !isMine && props.cell.nearMineCount > 0"
        :class="`number number-${props.cell.nearMineCount}`">
        {{ props.cell.nearMineCount }}
      </span>
      <span v-if="isFlag" class="flag">🚩</span>
      <span v-if="isQuestion" class="question">?</span>
    </div>

    <div class="coordinates" v-if="isHovered && !isOpen">
      {{ props.cell.row }},{{ props.cell.col }}
    </div>
  </div>
</template>

<script setup>
import { ref, defineProps, defineEmits, computed } from "vue";

const props = defineProps({
  cell: Object
});

const emit = defineEmits(['on-cell-open', 'on-cell-flag', 'on-cell-open-many']);

const isHovered = ref(false);

const isMine = computed(() => Boolean(props.cell.isMine));
const isOpen = computed(() => Boolean(props.cell.isOpen));
const isFlag = computed(() => Boolean(props.cell.isFlag));
const isQuestion = computed(() => Boolean(props.cell.isQuestion));

const cellClasses = computed(() => {
  const classes = [];

  if (isOpen.value) {
    classes.push('open');
    if (isMine.value) {
      classes.push('mine');
    } else if (props.cell.nearMineCount > 0) {
      classes.push(`number-${props.cell.nearMineCount}`);
    }
  } else {
    classes.push('closed');
    if (isFlag.value) {
      classes.push('flagged');
    } else if (isQuestion.value) {
      classes.push('question');
    }
  }

  return classes;
});

const handleClick = (event) => {
  if (isOpen.value || isFlag.value) return;

  emit('on-cell-open', props.cell);
};

const handleRightClick = (event) => {
  if (isOpen.value) return;
  
  emit('on-cell-flag', props.cell, isFlag.value);
};

const mouseMiddleButtonId = 1;
const handleMouseDown = (event) => {
  if (event.button === mouseMiddleButtonId) {
    event.currentTarget.classList.add('middle-click-pressed');
  }
};

const handleMouseUp = (event) => {
  if (event.button === mouseMiddleButtonId) {
    event.currentTarget.classList.remove('middle-click-pressed');
    handleMiddleClick(event);
  }
};

const handleMiddleClick = (event) => {
  event.preventDefault();

  if (!isOpen.value || isFlag.value) {
    return;
  }

  // Эмуляция двойного клика для открытия соседних ячеек
  // или специальная логика для среднего клика
  emit('on-cell-open-many', props.cell);
};
</script>

<style scoped lang="scss">
.cell {
  width: 32px;
  height: 32px;
  display: flex;
  align-items: center;
  justify-content: center;
  font-weight: bold;
  font-size: 14px;
  cursor: pointer;
  border: 3px solid;
  user-select: none;
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  position: relative;
  transition: all 0.15s ease;

  // Закрытая ячейка
  &.closed {
    background: linear-gradient(145deg, #c0c0c0, #d0d0d0);
    border-color: #ffffff #808080 #808080 #ffffff;
    box-shadow:
      inset -2px -2px 0px #808080,
      inset 2px 2px 0px #ffffff;

    &:hover {
      background: linear-gradient(145deg, #d0d0d0, #e0e0e0);
      transform: scale(1.05);
    }

    &:active {
      border-color: #808080 #ffffff #ffffff #808080;
      box-shadow:
        inset 2px 2px 0px #808080,
        inset -2px -2px 0px #ffffff;
      transform: scale(0.98);
    }
  }

  // Открытая ячейка
  &.open {
    background: #e8e8e8;
    border: 1px solid #b0b0b0;
    box-shadow: none;
    transform: scale(1);

    &:hover {
      background: #f0f0f0;
    }
  }

  // Ячейка с миной
  &.mine {
    background: #ff4444;
    animation: mineReveal 0.3s ease-out;

    .mine {
      font-size: 16px;
      animation: bounce 0.5s ease;
    }
  }

  // Ячейка с флагом
  &.flagged {
    .flag {
      font-size: 14px;
      animation: flagWave 0.5s ease;
    }
  }

  // Ячейка с вопросом
  &.question {
    .question {
      color: #0000ff;
      font-weight: 900;
      animation: pulse 1s infinite;
    }
  }

  // Стиль для нажатия среднего клика
  &.middle-click-pressed {
    background: linear-gradient(145deg, #a0a0a0, #b0b0b0);
    border-color: #808080 #ffffff #ffffff #808080;
    transform: scale(0.95);
    box-shadow:
      inset 2px 2px 0px #808080,
      inset -2px -2px 0px #ffffff;
  }

  // Цвета для цифр
  &.number-1 {
    color: #0000ff;
  }

  &.number-2 {
    color: #008000;
  }

  &.number-3 {
    color: #ff0000;
  }

  &.number-4 {
    color: #000080;
  }

  &.number-5 {
    color: #800000;
  }

  &.number-6 {
    color: #008080;
  }

  &.number-7 {
    color: #000000;
  }

  &.number-8 {
    color: #808080;
  }
}

.cell-content {
  display: flex;
  align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;

  .number {
    font-weight: 800;
    font-size: 16px;
  }
}

.coordinates {
  position: absolute;
  bottom: 1px;
  right: 1px;
  font-size: 8px;
  color: #666;
  background: rgba(255, 255, 255, 0.7);
  padding: 1px 2px;
  border-radius: 2px;
  pointer-events: none;
}

// Анимации
@keyframes cellOpen {
  0% {
    transform: scale(0.8);
    opacity: 0.7;
  }

  100% {
    transform: scale(1);
    opacity: 1;
  }
}

@keyframes mineReveal {
  0% {
    transform: scale(0.5);
    background: #ff0000;
  }

  50% {
    transform: scale(1.1);
  }

  100% {
    transform: scale(1);
    background: #ff4444;
  }
}

@keyframes flagWave {
  0% {
    transform: rotate(-10deg) scale(0.8);
  }

  50% {
    transform: rotate(5deg) scale(1.1);
  }

  100% {
    transform: rotate(0deg) scale(1);
  }
}

@keyframes bounce {

  0%,
  100% {
    transform: scale(1);
  }

  50% {
    transform: scale(1.2);
  }
}

@keyframes pulse {

  0%,
  100% {
    opacity: 1;
  }

  50% {
    opacity: 0.5;
  }
}

@keyframes middleClick {
  0% {
    background: #c0c0c0;
    transform: scale(1);
  }

  50% {
    background: #a0a0a0;
    transform: scale(0.95);
  }

  100% {
    background: #c0c0c0;
    transform: scale(1);
  }
}

// Адаптивность
@media (max-width: 768px) {
  .cell {
    width: 28px;
    height: 28px;
    font-size: 12px;

    .cell-content .number {
      font-size: 14px;
    }

    .mine {
      font-size: 14px;
    }

    .flag {
      font-size: 12px;
    }
  }
}

@media (max-width: 480px) {
  .cell {
    width: 24px;
    height: 24px;
    font-size: 10px;

    .cell-content .number {
      font-size: 12px;
    }

    .mine {
      font-size: 12px;
    }

    .flag {
      font-size: 10px;
    }

    .coordinates {
      font-size: 7px;
    }
  }
}
</style>