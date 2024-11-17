<script setup lang="ts">
import { ref } from 'vue'
import dingSound from '../sounds/mixkit-achievement-completed-2068.wav';

const newTask = ref('');
const tasks = ref([]);
const isRunning = ref(false);
const minutes = ref(25);
const seconds = ref(0);
let timerInterval;

const addTask = () => {
  if (newTask.value.trim() !== '') {
    tasks.value.push({
      title: newTask.value,
      completed: false,
    })
    newTask.value = ''
  }
}

const deleteTask = (index) => {
  tasks.value.splice(index, 1)
}

const playDing = () => {
    const audio = new Audio(dingSound);
    audio.addEventListener('canplaythrough', () => {
        audio.play().catch(error => {
            console.error('Error playing audio:', error);
        });
    });
};

const toggleTimer = () => {
    if (isRunning.value) {
        clearInterval(timerInterval);
    } else {
        timerInterval = setInterval(() => {
            if (seconds.value === 0) {
                if (minutes.value === 0) {
                    clearInterval(timerInterval);
                    playDing();
                } else {
                    minutes.value--;
                    seconds.value = 59;
                }
            } else {
                seconds.value--;
            }
        }, 1000);
    }
    isRunning.value = !isRunning.value;
};

const resetTimer = () => {
    clearInterval(timerInterval);
    isRunning.value = false;
    minutes.value = 25;
    seconds.value = 0;
};
</script>

<template>
    <header>
        <h1>PomTom Planner</h1>
        <img src="../assets/tomato.png" alt="Tomato" />
        <button @click="playDing">Ding!</button>
    </header>
<div>
    <h2>Timer:</h2>
    <div>{{ minutes }}:{{ seconds === 0 ? '00' : seconds }}</div>
    <button @click="toggleTimer">{{ isRunning ? 'Pause' : 'Start' }}</button>
    <button @click="resetTimer">Restart</button>
</div>
  <div>
    <h2>To Do:</h2>
    <ul>
      <li v-for="(task, index) in tasks.filter(task => !task.completed)" :key="task.title">
        <input type="checkbox" v-model="task.completed" />
        <span>{{ task.title }}</span>
        <button @click="deleteTask(index)">x</button>
      </li>
    </ul>
    <form @submit.prevent="addTask">
      <input type=text id="newTask" name="newTask" v-model="newTask" />
      <button type="submit">Add Task</button>
    </form>
  </div>
  <div>
    <h2>Completed:</h2>
    <ul>
      <li v-for="(task, index) in tasks.filter(task => task.completed)" :key="task.title">
        <input type="checkbox" v-model="task.completed" />
        <span>{{ task.title }}</span>
        <button @click="deleteTask(index)">x</button>
      </li>
    </ul>
  </div>
</template>

<style scoped>

</style>