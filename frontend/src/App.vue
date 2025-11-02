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
              {{ task.title }}
              <button @click="changeStatus(task, 'doing')">➡️</button>
            </div>
          </td>
          <td>
            <div
              v-for="(task, index) in tasks.filter(t => t.status === 'doing')"
              :key="index"
              class="task doing"
            >
              {{ task.title }}
              <button @click="changeStatus(task, 'done')">➡️</button>
            </div>
          </td>
          <td>
            <div
              v-for="(task, index) in tasks.filter(t => t.status === 'done')"
              :key="index"
              class="task done"
            >
              {{ task.title }}
            </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from "vue";

const newTask = ref("");
const tasks = ref([]);
const API_URL = "http://localhost:3000/tasks";

// 🔹 Obtener todas las tareas del backend
const getTasks = async () => {
  try {
    const res = await fetch(API_URL);
    tasks.value = await res.json();
  } catch (err) {
    console.error("Error cargando tareas:", err);
  }
};

// 🔹 Crear una nueva tarea
const addTask = async () => {
  if (newTask.value.trim() === "") return;
  try {
    const res = await fetch(API_URL, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ title: newTask.value }),
    });
    if (!res.ok) throw new Error("Error al crear tarea");
    newTask.value = "";
    await getTasks();
  } catch (err) {
    console.error(err);
    alert("Error creando tarea");
  }
};

// 🔹 Cambiar el estado de una tarea (todo → doing → done)
const changeStatus = async (task, newStatus) => {
  try {
    const res = await fetch(`${API_URL}/${task.id}`, {
      method: "PUT",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({ ...task, status: newStatus }),
    });
    if (!res.ok) throw new Error("Error al actualizar estado");
    await getTasks();
  } catch (err) {
    console.error(err);
  }
};

onMounted(getTasks);
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
  border-radius: 5px;
  border: 1px solid #ccc;
}

button {
  padding: 8px 12px;
  margin-left: 10px;
  cursor: pointer;
  border: none;
  border-radius: 5px;
  font-weight: bold;
}

.task-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  border: 3px solid #000;
}

th,
td {
  border: 2px solid #000;
  padding: 10px;
  vertical-align: top;
  width: 33%;
  text-align: center;
}

th {
  background-color: #000;
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
  display: flex;
  justify-content: space-between;
  align-items: center;
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

button:hover {
  opacity: 0.8;
}
</style>