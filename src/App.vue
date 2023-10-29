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

  // Event to remove todo
  const removeTodo = todo => {
    todos.value = todos.value.filter(t => t !== todo);
  }

  // Event to remove all todos
  const removeAllTodos = () => {
    todos.value = [];
  }

</script>

<template>
  <main class="app">

    <GreetingSection />

    <CreateTodo :todos="todos" />

    <TodoList 
      :todos="todos" 
      :isEmpty="isEmpty" 
      @remove="removeTodo" 
      @removeAll="removeAllTodos"
    />

  </main>
</template>