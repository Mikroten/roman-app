<script setup>
import {onUnmounted, ref} from 'vue';
import QuizComponent from './components/QuizComponent.vue';
import Modal from './components/Modal.vue';

const totalQuizzes = 118;
const correctCount = ref(0);
const incorrectCount = ref(0);
const answeredCount = ref(0);

const timerInterval = ref(null);
const startTime = ref(null);
const minutes = ref(0);
const seconds = ref(0);

const resetKey = ref(0);
const showModal = ref(false);

function updateScore(isCorrect) {
  if (isCorrect) {
    correctCount.value++;
  } else {
    incorrectCount.value++;
  }
}

function handleAnswerSelected() {
  answeredCount.value++;
  if (answeredCount.value === 1) {
    startTimer();
  }
  if (answeredCount.value === totalQuizzes) {
    showModal.value = true;
    stopTimer();
  }
}

function startTimer() {
  startTime.value = Date.now();
  timerInterval.value = setInterval(() => {
    const elapsedTime = Date.now() - startTime.value;
    minutes.value = Math.floor(elapsedTime / 60000);
    seconds.value = Math.floor((elapsedTime % 60000) / 1000);
  }, 1000);
}

function resetQuiz() {
  correctCount.value = 0;
  incorrectCount.value = 0;
  answeredCount.value = 0;
  stopTimer();
  startTime.value = null;
  minutes.value = 0;
  seconds.value = 0;
  resetKey.value++;
}

function stopTimer() {
  clearInterval(timerInterval.value);
  timerInterval.value = null;
}

onUnmounted(() => {
  stopTimer();
});

</script>

<template>
  <aside class="stats">
    <p>Answered Quizzes: {{ answeredCount }} / {{totalQuizzes}}</p>
    <p>Correct Answers: {{ correctCount }}</p>
    <p>Incorrect Answers: {{ incorrectCount }}</p>
    <button @click="resetQuiz">Reset Quiz</button>
  </aside>
  <main>
    <div class="quiz-page" :key="resetKey">
      <QuizComponent
          v-for="n in totalQuizzes"
          :key="n"
          @update-score="updateScore"
          @answer-selected="handleAnswerSelected"
      />
    </div>
    <Modal :show="showModal" @close="showModal = false">
      <h2>Quiz Results</h2>
      <p>Time Taken: {{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}</p>
      <p>Correct Answers: {{ correctCount }}</p>
      <p>Incorrect Answers: {{ incorrectCount }}</p>
      <button @click="resetQuizzes">Reset Quiz</button>
    </Modal>
  </main>

  <aside class="timer">
    {{ minutes }}:{{ seconds < 10 ? '0' + seconds : seconds }}
  </aside>
</template>

<style scoped>
.quiz-page {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 20px;
  padding: 20px;
}

aside p {
  margin: 0;
}

aside.stats {
  width: 200px;
  font-size: 14px;
  position: fixed;
  left: 10px;
  top: 10px;
  background-color: white;
  padding: 10px;
  box-shadow: 0px -2px 5px rgba(0, 0, 0, 0.2);
  border: 3px solid black;
  border-radius: 10px;
  display: flex;
  flex-direction: column;
  justify-content: space-around;
}

aside.timer{
  position: fixed;
  top: 10px;
  right: 10px;
  font-size: 20px;
}

button{
  margin-top: 10px;
  height: 30px;
  border: 3px solid black;
  background: white;
  border-radius: 7px;
  cursor: pointer;
}

@media (max-width: 600px) {
  .quiz-page {
    padding: 10px;
  }

  aside.stats{
    display: none;
  }
}

@media print {
  aside.stats, aside.timer {
    display: none;
  }
}
</style>
