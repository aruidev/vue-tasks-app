<script setup lang="ts">
import { computed, ref } from 'vue';
import type { Task, TaskFilter } from './types.ts';
import TaskForm from './components/TaskForm.vue';
import TaskList from './components/TaskList.vue';

const message = ref("Vue Tasks App");
const tasks = ref<Task[]>([]);
const filter = ref<TaskFilter>("all");

const totalDone = computed(() => tasks
  .value
  .reduce((total, task) => task.done ? total + 1 : total, 0)
);

function addTask(newTask: string) {
  tasks.value.push({
    id: crypto.randomUUID(),
    title: newTask,
    done: false,
  });
}

function toggleDone(id: string) {
  const task = tasks.value.find((task) => task.id === id);
  if (task) {
    task.done = !task.done;
  }
}

function removeTask(id: string) {
  const index = tasks.value.findIndex((task) => task.id === id)
  if (index !== -1) {
    tasks.value.splice(index, 1);
  }
}
</script>

<template>
  <main>
    <h1>{{ message }}</h1>
    <TaskForm @add-task="addTask" />
    <h3 v-if="!tasks.length">Add a task to get started.</h3>
    <h3 v-else>{{ totalDone }} / {{ tasks.length }} tasks completed</h3>
    <div v-if="tasks.length" class="button-container">
      <button class="secondary" @click="filter = 'all'">All</button>
      <button class="secondary" @click="filter = 'todo'">Todo</button>
      <button class="secondary" @click="filter = 'done'">Done</button>
    </div>
    <TaskList :tasks @toggle-done="toggleDone" @remove-task="removeTask" />
  </main>
</template>

<style>
main {
  max-width: 800px;
  margin: 1rem auto;
}

.button-container {
    display: flex;
    justify-content: end;
    gap: 0.5rem;
}
</style>