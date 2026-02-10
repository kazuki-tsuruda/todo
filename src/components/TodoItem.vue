<script setup lang="ts">
import type { Todo } from "../types";

defineProps<{
  todo: Todo;
}>();

const emit = defineEmits<{
  toggle: [id: number];
  delete: [id: number];
}>();
</script>

<template>
  <li :class="['todo-item', { completed: todo.done }]">
    <div class="todo-checkbox" @click="emit('toggle', todo.id)"></div>
    <span class="todo-text">{{ todo.text }}</span>
    <button class="delete-btn" @click="emit('delete', todo.id)">&times;</button>
  </li>
</template>

<style scoped>
.todo-item {
  display: flex;
  align-items: center;
  gap: 12px;
  padding: 12px 16px;
  background: #fff;
  border-radius: 8px;
  margin-bottom: 8px;
  box-shadow: 0 1px 3px rgba(0, 0, 0, 0.06);
  animation: slideIn 0.2s ease-out;
}

@keyframes slideIn {
  from {
    opacity: 0;
    transform: translateY(-8px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.todo-item.completed .todo-text {
  text-decoration: line-through;
  color: #aaa;
}

.todo-checkbox {
  width: 22px;
  height: 22px;
  border: 2px solid #ccc;
  border-radius: 50%;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  transition: all 0.2s;
}

.todo-checkbox:hover {
  border-color: #5b7fff;
}

.todo-item.completed .todo-checkbox {
  background: #5b7fff;
  border-color: #5b7fff;
}

.todo-item.completed .todo-checkbox::after {
  content: "";
  width: 6px;
  height: 10px;
  border: solid #fff;
  border-width: 0 2px 2px 0;
  transform: rotate(45deg);
  margin-bottom: 2px;
}

.todo-text {
  flex: 1;
  font-size: 1rem;
  line-height: 1.4;
  word-break: break-word;
}

.delete-btn {
  width: 28px;
  height: 28px;
  border: none;
  border-radius: 6px;
  background: transparent;
  color: #ccc;
  font-size: 1.2rem;
  cursor: pointer;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  transition: all 0.2s;
  opacity: 0;
}

.todo-item:hover .delete-btn {
  opacity: 1;
}

.delete-btn:hover {
  background: #fee;
  color: #e74c3c;
}
</style>
