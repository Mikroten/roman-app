<script setup>
import {ref, computed, watch} from 'vue';
import { defineEmits } from 'vue';


import Box1 from '@/assets/svg/box-1.svg';
import Box2 from '@/assets/svg/box-2.svg';
import Box3 from '@/assets/svg/box-3.svg';
import Box4 from '@/assets/svg/box-4.svg';
import Box5 from '@/assets/svg/box-5.svg';
import Box6 from '@/assets/svg/box-6.svg';
import Box7 from '@/assets/svg/box-7.svg';
import Box8 from '@/assets/svg/box-8.svg';

const boxes = [
  {
    svg: Box1,
    answer: {
      direction: 'vertical',
      place: 4
    }
  },
  {
    svg: Box2,
    answer: {
      direction: 'vertical',
      place: 2
    }
  },
  {
    svg: Box3,
    answer: {
      direction: 'vertical',
      place: 1
    }
  },
  {
    svg: Box4,
    answer: {
      direction: 'vertical',
      place: 3
    }
  },
  {
    svg: Box5,
    answer: {
      direction: 'horizontal',
      place: 1
    }
  },
  {
    svg: Box6,
    answer: {
      direction: 'horizontal',
      place: 2
    }
  },
  {
    svg: Box7,
    answer: {
      direction: 'horizontal',
      place: 4
    }
  },
  {
    svg: Box8,
    answer: {
      direction: 'horizontal',
      place: 3
    }
  }
]

const emit = defineEmits(['update-score', 'answer-selected', 'reset']);
const isAnswerSelected = ref(false);

const selectedLine = ref({ direction: null, place: null });
const isCorrectSelection = ref(false);

const randomIndex = ref(Math.floor(Math.random() * boxes.length));
const randomBox = computed(() => boxes[randomIndex.value]);

function pickRandomBox() {
  randomIndex.value = Math.floor(Math.random() * boxes.length);
}

function selectLine(direction, place) {
  if (isAnswerSelected.value) {
    return;
  }

  selectedLine.value = { direction, place };
  isCorrectSelection.value = direction === randomBox.value.answer.direction && place === randomBox.value.answer.place;
  isAnswerSelected.value = true;

  emit('update-score', isCorrectSelection.value);
  emit('answer-selected', true);
}

function isSelected(direction, place, correctCheck) {
  return selectedLine.value.direction === direction &&
      selectedLine.value.place === place &&
      isCorrectSelection.value === correctCheck;
}

watch(() => emit('reset'), () => {
  isAnswerSelected.value = false;
  selectedLine.value = { direction: null, place: null }
});
</script>

<template>
  <div v-if="randomBox" class="svg-container grid-row">
    <img :src="randomBox.svg" alt="Random Box" class="box-image"/>

    <!-- Horizontal lines -->
    <div class="grid-box">
      <div v-for="i in 4" :key="'h' + i" class="center">
        <div
          :class="['center', 'pointer', 'answer-box', {
            correct: isSelected('horizontal', i, true),
            incorrect: isSelected('horizontal', i, false)
          }]"
          @click="selectLine('horizontal', i)"
        >
          <div class="horizontal-line"></div>
        </div>
      </div>
    </div>

    <!-- Vertical lines -->
    <div class="grid-box">
      <div v-for="i in 4" :key="'v' + i" class="center">
        <div
          :class="['center', 'pointer', 'answer-box', {
            correct: isSelected('vertical', i, true),
            incorrect: isSelected('vertical', i, false) }]"
          @click="selectLine('vertical', i)"
        >
          <div class="vertical-line"></div>
        </div>
      </div>
    </div>
  </div>
</template>


<style scoped media="all">
.svg-container{
  display: flex;
  flex-direction: row;
  align-items: center;
  margin-bottom: 30px;
}

.grid-row{
  width: 200px;
  margin-left: auto;
  margin-right: auto;
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: 1fr;
  grid-column-gap: 0px;
  grid-row-gap: 0px;
}

.grid-box{
  width: 50px;
  height: 50px;
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  grid-template-rows: repeat(2, 1fr);
  grid-column-gap: 0;
  grid-row-gap: 0;
}

.box-image{
  width: 50px;
  height: 50px;
}

.center{
  display: flex;
  justify-content: center;
  align-items: center;
}

.pointer{
  cursor: pointer;
}

.answer-box{
  width: 10px;
  height: 10px;
}

.horizontal-line{
  cursor: pointer;
  width: 15px;
  height: 1px;
  background: #000;
}
.vertical-line{
  cursor: pointer;
  width: 1px;
  height: 15px;
  background: #000;
}

.correct {
  border-radius: 50%;
  width: 10px;
  height: 10px;
  box-shadow: 0 0 0 2px green; /* Green circle for correct selection */
}

.incorrect {
  border-radius: 50%;
  width: 10px;
  height: 10px;
  box-shadow: 0 0 0 2px red; /* Red circle for incorrect selection */
}

@media print {
  body {
    -webkit-print-color-adjust: exact;
    color-adjust: exact;
  }
  .svg-container{
    margin-bottom: 10px;
  }

  .horizontal-line, .vertical-line {
    background-color: black !important;
    display: block !important;
  }

  .grid-row{
    width: 400px;
  }

  .grid-box{
    width: 100px;
    height: 100px;
  }

  .box-image{
    width: 100px;
  }
}
</style>