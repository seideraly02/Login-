<template>
  <div class="todo-app">
    <h1>ToDo Application</h1>

    <div class="todo-add">
      <input v-model="newTask" placeholder="Add a new task" @keyup.enter="addTask" />
      <button @click="addTask">+</button>
    </div>

    <!-- Список задач -->
    <ul class="task-list">
      <li v-for="(task, index) in sortedTasks" :key="task.id" :class="{ completed: task.completed }" class="task-item">
        <div class="task-text">
          <input className="checkbox" type="checkbox" v-model="task.completed" @change="saveTasks" />
          <span v-if="!task.isEditing">{{ task.text }}</span>
          <input v-if="task.isEditing" v-model="task.text" @keyup.enter="stopEditing(task)" class="task-edit-input" />
        </div>
        <div class="task-buttons">
          <button @click="editTask(task)" class="edit-btn">✏️</button>
          <button @click="deleteTask(index)" class="delete-btn">🗑️</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';

const newTask = ref('');
const tasks = ref([]);

// Загрузка данных из localStorage
onMounted(() => {
  const savedTasks = JSON.parse(localStorage.getItem('tasks')) || [];
  tasks.value = savedTasks;
});

// Сортировка задач: сначала невыполненные, потом выполненные
const sortedTasks = computed(() => {
  return tasks.value.slice().sort((a, b) => a.completed - b.completed);
});

// Добавление новой задачи
const addTask = () => {
  if (newTask.value.trim() === '') {
    alert('Task cannot be empty!');
    return;
  }
  tasks.value.push({
    id: Date.now(),
    text: newTask.value.trim(),
    completed: false,
    isEditing: false
  });
  newTask.value = '';
  saveTasks();
};

// Удаление задачи
const deleteTask = (index) => {
  tasks.value.splice(index, 1);
  saveTasks();
};

// Редактирование задачи
const editTask = (task) => {
  task.isEditing = true;
};

// Завершение редактирования
const stopEditing = (task) => {
  task.isEditing = false;
  saveTasks();
};

// Сохранение задач в localStorage
const saveTasks = () => {
  localStorage.setItem('tasks', JSON.stringify(tasks.value));
};
</script>

<style scoped>
.todo-app {
  max-width: 600px;
  margin: 30px auto;
  font-family: 'Roboto', sans-serif;
  padding: 20px;
  border-radius: 8px;
  box-shadow: 0 5px 15px rgba(0,0,0,0.1);
  background-color: #ffffff;
}

h1 {
  text-align: center;
  color: #4a4e69;
  margin-bottom: 20px;
  font-size: 24px;
}

.todo-add {
  display: flex;
  justify-content: space-between;
  margin-bottom: 20px;
}

.todo-add input {
  flex-grow: 1;
  padding: 10px;
  border: 2px solid #9a8c98;
  border-radius: 4px;
  font-size: 16px;
  color: #222;
}
.checkbox{
  cursor: pointer;
}
.todo-add button {
  padding: 10px 20px;
  background-color: #c9ada7;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.todo-add button:hover {
  background-color: #9a8c98;
}

.filters {
  text-align: center;
  margin-bottom: 20px;
}

.filters button {
  padding: 8px 16px;
  background-color: #f2e9e4;
  border: 1px solid #ccc;
  border-radius: 20px;
  cursor: pointer;
  margin: 0 5px;
  transition: background-color 0.3s;
}

.filters button.active,
.filters button:hover {
  background-color: #c9ada7;
  color: white;
}

/* Стили для списка задач */
.task-list {
  list-style-type: none;
  margin: 0;
  padding: 0;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #eae2b7;
  transition: background-color 0.3s;
}

.task-item:last-child {
  border-bottom: none;
}

.task-item.completed .task-text span {
  text-decoration: line-through;
  color: #6b705c;
  cursor: pointer;
}

.task-item:hover {
  background-color: #f2e9e4;
}

/* Стили для кнопок управления задачей */
.task-buttons button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 18px;
  color: #9a8c98;
}

/* Стили для поля редактирования задачи */
.task-edit-input {
  width: 100%;
  padding: 8px;
  font-size: 16px;
  border: 1px solid #9a8c98;
  border-radius: 4px;
  color: #222;
}

</style>
