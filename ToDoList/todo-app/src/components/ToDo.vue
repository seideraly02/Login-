<template>
  <div class="todo-app">
    <h1>ToDo Application</h1>

    <!-- Форма для добавления новой задачи -->
    <div class="todo-add">
      <input v-model="newTask" placeholder="Add a new task" @keyup.enter="addTask" />
      <button @click="addTask">+</button>
    </div>

    <!-- Фильтры для задач -->
    <div class="filters">
      <button @click="filterTasks('all')" :class="{ active: filter === 'all' }">All</button>
      <button @click="filterTasks('active')" :class="{ active: filter === 'active' }">Active</button>
      <button @click="filterTasks('completed')" :class="{ active: filter === 'completed' }">Completed</button>
    </div>

    <!-- Список задач -->
    <ul class="task-list">
      <li v-for="(task, index) in sortedTasks" :key="task.id" :class="{ completed: task.completed }" class="task-item">
        <div class="task-text">
          <input type="checkbox" v-model="task.completed" @change="saveTasks" />
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
const filter = ref('all');

// Загрузка данных из localStorage
onMounted(() => {
  const savedTasks = JSON.parse(localStorage.getItem('tasks')) || [];
  tasks.value = savedTasks;
});

// Фильтрация задач
const filteredTasks = computed(() => {
  if (filter.value === 'completed') {
    return tasks.value.filter(task => task.completed);
  } else if (filter.value === 'active') {
    return tasks.value.filter(task => !task.completed);
  }
  return tasks.value;
});

// Сортировка задач: сначала невыполненные, потом выполненные
const sortedTasks = computed(() => {
  return filteredTasks.value.slice().sort((a, b) => a.completed - b.completed);
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

// Фильтрация задач по статусу
const filterTasks = (status) => {
  filter.value = status;
};
</script>

<style scoped>
/* Общий стиль для приложения */
.todo-app {
  max-width: 500px;
  margin: 0 auto;
  font-family: 'Arial', sans-serif;
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  background-color: #f9f9f9;
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
}

/* Стиль формы для добавления задач */
.todo-add {
  display: flex;
  justify-content: center;
  margin-bottom: 20px;
}

.todo-add input {
  width: 70%;
  padding: 10px;
  border: 2px solid #ccc;
  border-radius: 5px;
  font-size: 16px;
  margin-right: 10px;
}

.todo-add button {
  background-color: #28a745;
  color: white;
  border: none;
  border-radius: 50%;
  width: 40px;
  height: 40px;
  font-size: 20px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.todo-add button:hover {
  background-color: #218838;
}

/* Стиль фильтров задач */
.filters {
  display: flex;
  justify-content: space-around;
  margin-bottom: 20px;
}

.filters button {
  padding: 10px;
  background-color: #f1f1f1;
  border: 1px solid #ccc;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.filters button.active,
.filters button:hover {
  background-color: #007bff;
  color: white;
}

/* Стиль списка задач */
.task-list {
  list-style-type: none;
  padding: 0;
}

.task-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 10px;
  border-bottom: 1px solid #ccc;
  transition: background-color 0.3s ease;
}

.task-item.completed .task-text span {
  text-decoration: line-through;
  color: #888;
}

.task-item:hover {
  background-color: #e9e9e9;
}

/* Стиль кнопок задач */
.task-buttons button {
  background: none;
  border: none;
  cursor: pointer;
  font-size: 16px;
  margin-left: 5px;
}

/* Стили для редактируемой задачи */
.task-edit-input {
  width: 80%;
  padding: 5px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 3px;
}
</style>
