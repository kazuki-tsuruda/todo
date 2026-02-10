<script setup lang="ts">
import { ref, computed, watch } from "vue";
import type { Todo, Filter } from "./types";
import TodoItem from "./components/TodoItem.vue";

const STORAGE_KEY = "todos";

const todos = ref<Todo[]>(
  JSON.parse(localStorage.getItem(STORAGE_KEY) ?? "[]")
);
const currentFilter = ref<Filter>("all");
const newTodoText = ref("");

watch(todos, (val) => {
  localStorage.setItem(STORAGE_KEY, JSON.stringify(val));
}, { deep: true });

const filteredTodos = computed(() => {
  return todos.value.filter((t) => {
    if (currentFilter.value === "active") return !t.done;
    if (currentFilter.value === "completed") return t.done;
    return true;
  });
});

const activeCount = computed(() => todos.value.filter((t) => !t.done).length);
const hasCompleted = computed(() => todos.value.some((t) => t.done));

const emptyMessage = computed(() => {
  if (currentFilter.value === "completed") return "完了済みのタスクはありません";
  if (currentFilter.value === "active") return "未完了のタスクはありません";
  return "タスクを追加してください";
});

const filters: { key: Filter; label: string }[] = [
  { key: "all", label: "すべて" },
  { key: "active", label: "未完了" },
  { key: "completed", label: "完了済み" },
];

function addTodo() {
  const text = newTodoText.value.trim();
  if (!text) return;
  todos.value.push({ id: Date.now(), text, done: false });
  newTodoText.value = "";
}

function toggleTodo(id: number) {
  const todo = todos.value.find((t) => t.id === id);
  if (todo) todo.done = !todo.done;
}

function deleteTodo(id: number) {
  todos.value = todos.value.filter((t) => t.id !== id);
}

function clearCompleted() {
  todos.value = todos.value.filter((t) => !t.done);
}
</script>

<template>
  <div class="container">
    <h1>TODO</h1>

    <form class="todo-form" @submit.prevent="addTodo">
      <input
        v-model="newTodoText"
        type="text"
        class="todo-input"
        placeholder="新しいタスクを入力..."
        autocomplete="off"
      />
      <button type="submit" class="add-btn">追加</button>
    </form>

    <div class="filters">
      <button
        v-for="f in filters"
        :key="f.key"
        :class="['filter-btn', { active: currentFilter === f.key }]"
        @click="currentFilter = f.key"
      >
        {{ f.label }}
      </button>
    </div>

    <ul class="todo-list">
      <TodoItem
        v-for="todo in filteredTodos"
        :key="todo.id"
        :todo="todo"
        @toggle="toggleTodo"
        @delete="deleteTodo"
      />
      <li v-if="filteredTodos.length === 0" class="empty-message">
        {{ emptyMessage }}
      </li>
    </ul>

    <div class="footer">
      <span>残り {{ activeCount }} 件</span>
      <button v-show="hasCompleted" class="clear-btn" @click="clearCompleted">
        完了済みを削除
      </button>
    </div>
  </div>
</template>

<style scoped>
.container {
  width: 100%;
  max-width: 520px;
  margin: 0 auto;
}

h1 {
  text-align: center;
  font-size: 2.4rem;
  font-weight: 700;
  color: #2c3e50;
  margin-bottom: 24px;
  letter-spacing: 0.1em;
}

.todo-form {
  display: flex;
  gap: 8px;
  margin-bottom: 16px;
}

.todo-input {
  flex: 1;
  padding: 12px 16px;
  font-size: 1rem;
  border: 2px solid #ddd;
  border-radius: 8px;
  outline: none;
  transition: border-color 0.2s;
}

.todo-input:focus {
  border-color: #5b7fff;
}

.add-btn {
  padding: 12px 20px;
  font-size: 1rem;
  font-weight: 600;
  color: #fff;
  background: #5b7fff;
  border: none;
  border-radius: 8px;
  cursor: pointer;
  transition: background 0.2s;
  white-space: nowrap;
}

.add-btn:hover {
  background: #4a6ae5;
}

.filters {
  display: flex;
  gap: 4px;
  margin-bottom: 16px;
}

.filter-btn {
  flex: 1;
  padding: 8px;
  font-size: 0.875rem;
  font-weight: 500;
  color: #777;
  background: transparent;
  border: 1px solid #ddd;
  border-radius: 6px;
  cursor: pointer;
  transition: all 0.2s;
}

.filter-btn:hover {
  color: #5b7fff;
  border-color: #5b7fff;
}

.filter-btn.active {
  color: #fff;
  background: #5b7fff;
  border-color: #5b7fff;
}

.todo-list {
  list-style: none;
  padding: 0;
}

.empty-message {
  text-align: center;
  color: #bbb;
  padding: 32px 0;
  font-size: 0.95rem;
}

.footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 12px 4px;
  font-size: 0.875rem;
  color: #999;
}

.clear-btn {
  font-size: 0.875rem;
  color: #999;
  background: none;
  border: none;
  cursor: pointer;
  transition: color 0.2s;
}

.clear-btn:hover {
  color: #e74c3c;
}
</style>
