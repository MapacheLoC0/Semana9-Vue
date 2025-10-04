<template>
  <div class="app">
    <h1>Gestor de Tareas</h1>

    <!-- Entrada para escribir la tarea -->
    <div class="task-input">
      <input 
        v-model="newTask" 
        @keyup.enter="addTask"
        placeholder="Escribe el nombre de la tarea"
      />
      <button @click="addTask">Agregar</button>
    </div>

    <!-- Lista de tareas -->
    <h2>Listado de tareas</h2>
    <p v-if="tasks.length === 0">No hay tareas registradas</p>

    <table v-else class="task-table">
      <thead>
        <tr>
          <th>To do</th>
          <th>Doing</th>
          <th>Done</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>
            <div
              v-for="(task, index) in tasks.filter(t => t.status === 'todo')"
              :key="index"
              class="task todo"
            >
              {{ task.name }}
              <button @click="changeStatus(task, 'doing')">➡️</button>
            </div>
          </td>
          <td>
            <div
              v-for="(task, index) in tasks.filter(t => t.status === 'doing')"
              :key="index"
              class="task doing"
            >
              {{ task.name }}
              <button @click="changeStatus(task, 'done')">➡️</button>
            </div>
          </td>
          <td>
            <div
              v-for="(task, index) in tasks.filter(t => t.status === 'done')"
              :key="index"
              class="task done"
            >
              {{ task.name }}
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref } from 'vue';

const newTask = ref('');
const tasks = ref([]);

function addTask() {
  if (newTask.value.trim() === '') return;
  tasks.value.push({ name: newTask.value, status: 'todo' });
  newTask.value = '';
}

function changeStatus(task, newStatus) {
  task.status = newStatus;
}
</script>

<style scoped>
.app {
  text-align: center;
  margin: 20px;
  font-family: Arial, sans-serif;
}

.task-input {
  margin-bottom: 20px;
}

input {
  padding: 8px;
  width: 250px;
}

button {
  padding: 8px 12px;
  margin-left: 10px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
}

.task-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  border: 3px solid #000;
}

th, td {
  border: 2px solid #000;
  padding: 10px;
  vertical-align: top;
  width: 33%;
  text-align: center;
}

th {
  background-color: white;
  font-size: 18px;
  font-weight: bold;
  border-bottom: 3px solid #000;
}

.task {
  margin: 8px 0;
  padding: 6px;
  border-radius: 5px;
  color: white;
  font-weight: bold;
}

.todo {
  background-color: #0075f3;
}
.doing {
  background-color: #20f300;
}
.done {
  background-color: #f30000;
}
</style>