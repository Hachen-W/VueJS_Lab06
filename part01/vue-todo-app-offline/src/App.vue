<template>
  <div class="todo-container">
    <h2>ToDo List</h2>
    
    <div class="input-group">
      <input 
        v-model="newTaskTitle" 
        placeholder="New Task" 
        @keyup.enter="handleCreate"
      />
      <button @click="handleCreate">Add</button>
    </div>

    <ul class="todo-list">
      <li 
        v-for="task in tasks" 
        :key="task.id" 
        :class="{ 'is-completed': task.completed }"
      >
        <div class="task-item-left">
          <input 
            type="checkbox" 
            :checked="task.completed" 
            @change="handleToggle(task)"
          />
          <span>{{ task.title }}</span>
        </div>
        <button class="delete-btn" @click="triggerDeleteConfirmation(task.id)">
          <span class="trash-text">✖</span>
        </button>
      </li>
    </ul>

    <Popup :isOpen="isPopupOpen" @close="cancelDelete">
      <div class="delete-confirm-box">
        <div class="warning-icon">!</div>
        <h3>Delete?</h3>
        <p>Please ensure and then confirm!</p>
        <div class="popup-buttons">
          <button class="btn-cancel" @click="cancelDelete">No, cancel!</button>
          <button class="btn-confirm" @click="handleDelete">Yes, delete it!</button>
        </div>
      </div>
    </Popup>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import Popup from './components/Popup.vue';

const tasks = ref([]);
const newTaskTitle = ref('');
const isPopupOpen = ref(false);
const taskIdToDelete = ref(null);

// Чтение данных из локальной памяти браузера
onMounted(() => {
  const cachedTasks = localStorage.getItem('todo_tasks');
  if (cachedTasks) {
    tasks.value = JSON.parse(cachedTasks);
  }
});

// Сохранение в локальную память браузера
const saveToCache = () => {
  localStorage.setItem('todo_tasks', JSON.stringify(tasks.value));
};

// Создание задачи
const handleCreate = () => {
  if (!newTaskTitle.value.trim()) return;

  tasks.value.unshift({
    id: Date.now(),
    title: newTaskTitle.value,
    completed: false
  });
  
  saveToCache();
  newTaskTitle.value = '';
};

// Изменение статуса
const handleToggle = (task) => {
  task.completed = !task.completed;
  saveToCache();
};

// Подготовка к удалению
const triggerDeleteConfirmation = (id) => {
  taskIdToDelete.value = id;
  isPopupOpen.value = true;
};

const cancelDelete = () => {
  isPopupOpen.value = false;
  taskIdToDelete.value = null;
};

// Удаление
const handleDelete = () => {
  const id = taskIdToDelete.value;
  if (!id) return;

  tasks.value = tasks.value.filter(task => task.id !== id);
  saveToCache();
  cancelDelete();
};
</script>

<style>
body {
  font-family: sans-serif;
  background-color: #f8f9fa;
  margin: 0;
  padding: 40px 10px;
}

.todo-container {
  max-width: 600px;
  margin: 0 auto;
  background: white;
  padding: 20px;
  border-radius: 6px;
  box-shadow: 0 1px 3px rgba(0,0,0,0.1);
}

h2 {
  color: #333;
  font-weight: 400;
  margin-top: 0;
}

.input-group { display: flex; gap: 10px; margin-bottom: 20px; }
.input-group input { flex: 1; padding: 10px 12px; border: 1px solid #ced4da; border-radius: 4px; font-size: 15px; }
.input-group button { background-color: #3b71ca; color: white; border: none; padding: 0 20px; border-radius: 4px; cursor: pointer; font-size: 15px; font-weight: 500; }

.todo-list { list-style: none; padding: 0; margin: 0; }
.todo-list li { display: flex; justify-content: space-between; align-items: center; padding: 14px 10px; border-bottom: 1px solid #f1f3f5; }
.task-item-left { display: flex; align-items: center; gap: 12px; }
.task-item-left input[type="checkbox"] { width: 16px; height: 16px; cursor: pointer; }
.is-completed span { text-decoration: line-through; color: #adb5bd; }

.delete-btn { background: transparent; border: none; cursor: pointer; padding: 4px; }
.trash-text { color: #e74c3c; font-size: 16px; font-weight: bold; }

.delete-confirm-box { text-align: center; }
.warning-icon { width: 60px; height: 60px; border: 3px solid #f8bb86; border-radius: 50%; display: flex; align-items: center; justify-content: center; margin: 0 auto 15px; color: #f8bb86; font-size: 40px; font-weight: 600; }
.delete-confirm-box h3 { font-size: 26px; color: #595959; margin: 0 0 10px 0; }
.delete-confirm-box p { color: #545454; font-size: 16px; margin-bottom: 25px; }
.popup-buttons { display: flex; justify-content: center; gap: 12px; }
.btn-cancel { background-color: #aaa; color: white; border: none; padding: 10px 20px; font-size: 15px; border-radius: 4px; cursor: pointer; }
.btn-confirm { background-color: #3085d6; color: white; border: none; padding: 10px 20px; font-size: 15px; border-radius: 4px; cursor: pointer; }
</style>
