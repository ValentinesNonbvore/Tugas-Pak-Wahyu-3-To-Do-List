<script setup>
import { ref, onMounted } from 'vue';
import 'bootstrap/dist/css/bootstrap.css';

const tasks = ref([]);
const newTask = ref('');
let updateNote = ref('');
let count = ref(0);

const loadTasks = () => {
  const taskKeys = Object.keys(localStorage).sort();
  tasks.value = taskKeys.map((key) => ({
    id: key,
    name: key.split('_')[1],
    completed: JSON.parse(localStorage.getItem(key)),
  }));
};

const displayTasks = () => {
  tasks.value.length > 0 ? (tasksDiv.style.display = 'inline-block') : (tasksDiv.style.display = 'none');
};

const addTask = () => {
  if (newTask.value.trim() === '') {
    alert('Please Enter A Task');
    return;
  }

  if (updateNote.value === '') {
    // New task
    const newTaskId = `${count.value}_${newTask.value}`;
    updateStorage(newTaskId, newTask.value, false);
    count.value += 1;
  } else {
    // Update existing task
    const existingCount = updateNote.value.split('_')[0];
    removeTask(updateNote.value);
    updateStorage(`${existingCount}_${newTask.value}`, newTask.value, false);
    updateNote.value = '';
  }

  newTask.value = '';
};

const editTask = (task) => {
  updateNote.value = task.id;
  newTask.value = task.name;
};

const deleteTask = (task) => {
  removeTask(task.id);
};

const toggleTaskCompletion = (task) => {
  const updatedCompletion = !task.completed;
  updateStorage(task.id, task.name, updatedCompletion);
};

const updateStorage = (id, name, completed) => {
  localStorage.setItem(id, JSON.stringify(completed));
  loadTasks();
};

const removeTask = (id) => {
  localStorage.removeItem(id);
  loadTasks();
};

onMounted(() => {
  updateNote.value = '';
  count.value = Object.keys(localStorage).length;
  loadTasks();
});
</script>

<template>
  <div class="container" style="width:100vh">
    <h1>&#10066; To-do list</h1>

    <div id="new-task">
      <input v-model="newTask" type="text" placeholder="Enter The Task Here..." />
      <button @click="addTask">Add</button>
    </div>

    <div id="tasks" v-if="tasks.length">
      <div
        v-for="task in tasks"
        :key="task.id"
        :id="task.id"
        :class="['task', { completed: task.completed }]"
        @click="toggleTaskCompletion(task)"
      >
        <span id="taskname">{{ task.name }}</span>
        <button
          class="edit"
          v-if="!task.completed"
          @click.stop="editTask(task)"
        >
          <i class="fa-solid fa-pen-to-square"></i>
        </button>
        <button class="delete" @click.stop="deleteTask(task)">
          <i class="fa-solid fa-trash"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
/* Add your styles here */
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
body {
  background-color: #ab20fd;
  display: flex;
  justify-content: center;
}
h1 {
  color: black;
  font-family: 'Poppins', sans-serif;
  font-size: 2em;
  font-weight: 700;
  display: block;
}
.container {
  width: 90%;
  max-width: 34em;
}
#new-task {
  position: relative;
  width: 100%;
  background-color: #ffffff;
  padding: 1.8em 1.25em;
  border-radius: 0.3em;
  box-shadow: 0 1.25em 1.8em rgba(1, 24, 48, 0.15);
  display: flex;
  gap: 1em;
}
#new-task input {
  font-family: 'Poppins', sans-serif;
  font-size: 1em;
  border: none;
  border-bottom: 2px solid #d1d3d4;
  padding: 0.8em 0.5em;
  color: #111111;
  font-weight: 500;
  flex: 1;
}
#new-task input:focus {
  outline: none;
  border-color: black;
}
#new-task button {
  font-family: 'Poppins', sans-serif;
  font-weight: 500;
  font-size: 1em;
  background-color: black;
  color: #ffffff;
  outline: none;
  border: none;
  border-radius: 0.3em;
  cursor: pointer;
  width: 6.5em;
}
#tasks {
  background-color: #ffffff;
  position: relative;
  padding: 1.8em 1.25em;
  margin-top: 1em;
  width: 100%;
  box-shadow: 0 1.25em 1.8em rgba(1, 24, 48, 0.15);
  border-radius: 0.6em;
}
.marginBottom {
  margin-bottom: 7em;
}
.task {
  background-color: #ffffff;
  padding: 0.3em 0.6em;
  margin-top: 0.6em;
  display: flex;
  align-items: center;
  border-bottom: 2px solid #d1d3d4;
  cursor: pointer;
}
.task span {
  font-family: 'Poppins', sans-serif;
  font-size: 0.9em;
  font-weight: 400;
}
.task button {
  color: #ffffff;
  padding: 0.8em 0;
  width: 2.8em;
  border-radius: 0.3em;
  border: none;
  outline: none;
  cursor: pointer;
}
.delete {
  background-color: red;
}
.edit {
  background-color: black;
  margin-left: auto;
  margin-right: 1em;
}
.completed {
  text-decoration: line-through;
}
.social-icons {
  display: flex;
  gap: 10px; /* Adjust the gap between social icons */
  position: absolute;
  bottom: 20px; /* Adjust the distance from the bottom */
  z-index: 4;
  left: 46.5%;
}
.social-icons img {
  width: 40px; /* Adjust the size of the icons */
  height: 40px;
  transition: transform 0.2s ease; /* Add a smooth transition on hover */
}
.social-icons img:hover {
  transform: translateY(-5px); /* Move the icons up slightly on hover */
}
</style>