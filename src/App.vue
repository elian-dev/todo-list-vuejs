<script setup>
  import { ref, onMounted, computed, watch } from 'vue';
  import GreetingSection from './components/GreetingSection.vue';
  import CreateTodo from './components/CreateTodo.vue';
  import TodoList from './components/TodoList.vue';

  const todos = ref([]);
  const isEmpty = ref(true);

  watch(todos, newVal => {
    localStorage.setItem('todos', JSON.stringify(newVal));
    isEmpty.value =  todos.value.length === 0;
  }, {deep: true});

  onMounted(() => {
    todos.value = JSON.parse(localStorage.getItem('todos') || []);
    isEmpty.value =  todos.value.length === 0;
  });

</script>

<template>
  <main class="app">

    <GreetingSection />

    <CreateTodo :todos="todos" />

    <TodoList :todos="todos" :isEmpty="isEmpty" />

  </main>
</template>