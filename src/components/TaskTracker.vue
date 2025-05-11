<script setup lang="ts">
import { ref, computed } from "vue";
import { useLocalStorage } from "@vueuse/core";

interface Task {
  name: string;
  isCompleted: boolean;
  date: Date;
}

const tasks = useLocalStorage<Task[]>("tasks", []);

const newTask = ref<Omit<Task, "date">>({
  name: "",
  isCompleted: false,
});

const completedTasks = computed(() => {
  return tasks.value.filter((task) => task.isCompleted).length;
});

const addTask = () => {
  if (newTask.value.name.trim() !== "") {
    const taskToAdd: Task = {
      ...newTask.value,
      date: new Date(),
    };
    tasks.value.push(taskToAdd);
    newTask.value.name = "";
  }
};

const removeTask = (index: number) => {
  tasks.value.splice(index, 1);
};

const handleKeyDown = (event: KeyboardEvent) => {
  if (event.key === "Enter") {
    addTask();
  }
};

const formatDate = (date: Date | string): string => {
  const d = new Date(date);
  const day = String(d.getDate()).padStart(2, "0");
  const month = String(d.getMonth() + 1).padStart(2, "0");
  const year = d.getFullYear();
  const hours = String(d.getHours()).padStart(2, "0");
  const minutes = String(d.getMinutes()).padStart(2, "0");
  const seconds = String(d.getSeconds()).padStart(2, "0");
  return `${day}.${month}.${year} ${hours}:${minutes}:${seconds}`;
};
</script>

<template>
  <div class="task-tracker">
    <h1>Task Tracker</h1>
    <div class="input-group">
      <input
        @keydown="handleKeyDown"
        type="text"
        v-model="newTask.name"
        placeholder="Enter a task"
      />
      <button @click="addTask">Add Task</button>
    </div>
    <ul class="task-list">
      <li
        class="task-list-element"
        v-for="(task, index) in tasks"
        :key="index"
        :class="task.isCompleted ? 'completed' : ''"
        @click="task.isCompleted = !task.isCompleted"
      >
        <div class="task-info">
          <input
            :id="`taskIsCompleted-${index}`"
            type="checkbox"
            v-model="task.isCompleted"
          />
          <span :class="{ 'task-completed': task.isCompleted }">{{
            task.name
          }}</span>
          <span class="task-date">{{ formatDate(task.date) }}</span>
        </div>
        <button @click.stop="removeTask(index)">Remove</button>
      </li>
    </ul>
    <p>Total Tasks: {{ tasks.length }}</p>
    <p>Completed Tasks: {{ completedTasks }}</p>
    <p>Pending Tasks: {{ tasks.length - completedTasks }}</p>
  </div>
</template>

<style scoped>
.task-tracker {
  font-family: "Arial", sans-serif;
  max-width: 600px;
  margin: 20px auto;
  padding: 20px;
  background-color: #f9f9f9;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

h1 {
  text-align: center;
  color: #333;
  margin-bottom: 20px;
}

.input-group {
  display: flex;
  margin-bottom: 20px;
}

.input-group input[type="text"] {
  flex-grow: 1;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px 0 0 4px;
  font-size: 16px;
}

.input-group button {
  padding: 10px 15px;
  background-color: #5cb85c;
  color: white;
  border: none;
  border-radius: 0 4px 4px 0;
  cursor: pointer;
  font-size: 16px;
  transition: background-color 0.3s ease;
}

.input-group button:hover {
  background-color: #4cae4c;
}

.task-list {
  list-style-type: none;
  padding: 0;
  margin-bottom: 20px;
}

.task-list-element {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  background-color: #fff;
  border: 1px solid #eee;
  border-radius: 4px;
  margin-bottom: 8px;
  transition: background-color 0.3s ease;
}

.task-completed {
  text-decoration: line-through;
  color: #aaa;
}

.task-info {
  display: flex;
  align-items: center;
  flex-grow: 1;
  gap: 10px;
}

.task-date {
  font-size: 0.8em;
  color: #666;
  white-space: nowrap;
}

.task-list-element:last-child {
  margin-bottom: 0;
}

.task-list-element input[type="checkbox"] {
  margin-right: 10px;
  cursor: pointer;
}

.task-list-element span {
  flex-grow: 1;
}

.task-list-element button {
  background-color: #d9534f;
  color: white;
  border: none;
  padding: 6px 10px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 14px;
  margin-left: 10px;
  transition: background-color 0.3s ease;
}

.task-list-element button:hover {
  background-color: #c9302c;
}

.completed span {
  text-decoration: line-through;
  color: #aaa;
}

.task-list-element.completed {
  background-color: #f0f0f0;
}

p {
  color: #555;
  font-size: 16px;
  line-height: 1.6;
}
</style>
