<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const todos = ref([]);
const name = ref('');
const inputContent = ref('');
const inputCategory = ref(null);

const todosAsync = computed(() =>
  todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  })
);

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});

const addTodo = () => {
  if (inputContent.value.trim() === '' || inputCategory.value === null) return;

  todos.value.push({
    content: inputContent.value,
    category: inputCategory.value,
    createdAt: new Date().getTime(),
    done: false,
  });

  inputContent.value = '';
  inputCategory.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

watch(name, (newValue) => {
  localStorage.setItem('name', newValue);
});

watch(
  todos,
  (newValue) => {
    localStorage.setItem('todos', JSON.stringify(newValue));
  },
  { deep: true }
);

// return { todos, name, inputContent, inputCategory, addTodo, todosAsync };
</script>
<template>
  <nav class="nav">
    <h1>Vue Todo List</h1>
  </nav>
  <main class="app">
    <section class="create-todo">
      <h3>Create a Todo</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your to-do list?</h4>
        <input
          type="text"
          placeholder="e.g. Make a video"
          v-model="inputContent" />

        <h4>Select a Category</h4>

        <div class="options">
          <label>
            <input
              type="radio"
              name="category"
              value="business"
              v-model="inputCategory" />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>
          <label>
            <input
              type="radio"
              name="category"
              value="personal"
              v-model="inputCategory" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </div>
        <input type="submit" class="submit" value="Add Todo" />
      </form>
    </section>
    <section class="todo-list">
      <h3>Todo List</h3>
      <div class="list">
        <div
          v-for="todo in todosAsync"
          :key="todo.createdAt"
          :class="`todo-item ${todo.done && 'done'} `">
          <label>
            <input type="checkbox" v-model="todo.done" />
            <span :class="`bubble ${todo.category}`"></span>
          </label>
          <div class="todo-content">
            <input type="text" v-model="todo.content" />
          </div>
          <div class="actions">
            <button class="delete" @click="removeTodo(todo)">Delete</button>
          </div>
        </div>
      </div>
    </section>
  </main>
</template>

<style scoped></style>
