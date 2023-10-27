<script setup>
  import { ref, onMounted, computed, watch } from 'vue'
  import GreetingSection from './components/GreetingSection.vue';
  import EmptyDraw from './assets/empty.svg';

  const todos = ref([]);
  const isEmpty = ref(true);
  const isSelectedAll = ref(false);
  const isAllDone = ref(false);

  const input_content = ref('');
  const input_category = ref(null);

  const todos_asc = computed(() => todos.value.sort((a, b) => {
    return b.createdAt - a.createdAt;
  }));

  const addTodo = () => {
    if(input_content.value.trim() === '' || input_category.value === null) {
      return
    };

    todos.value.push({
      content: input_content.value.trim(),
      category: input_category.value,
      done: false,
      createdAt: new Date().getTime()
    })

    input_content.value = ''
    input_category.value = null
  };

  const removeTodo = todo => {
    const confirm = window.confirm("Are you sure to delete this item? \nThis action can be reverted");
    
    if(confirm) {
      todos.value = todos.value.filter(t => t !== todo)
    } else {
      return;
    }
  }

  const markDone = () => {
    todos.value.map((todo) => {
      todo.done = !isAllDone.value && true;
    });

    isAllDone.value = !isAllDone.value && true;

  }

  const deleteAll = () => {
    const confirm = window.confirm("Are you sure to delete all items? \nThis action can be reverted");
    
    if(confirm) {
      todos.value = [];
    } else {
      return;
    }
  }

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
    
    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form @submit.prevent="addTodo">
        <h4>What's on your todo list?</h4>
        
        <input 
          type="text" 
          placeholder="e.g. Make a video" 
          v-model="input_content"
        />

        <h4>Pick a category</h4>

        <article class="options">
          <label>
            <input 
              type="radio"
              name="category"
              value="business"
              v-model="input_category"
            />
            <span class="bubble business"></span>
            <div>Business</div>
          </label>

          <label>
            <input 
              type="radio"
              name="category"
              value="personal"
              v-model="input_category"
            />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>
        </article>

        <input type="submit" value="Add todo">
      </form>

    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list">

        <article class="empty" v-if="isEmpty">
          <h2>Your task list is empty!</h2>
          <img :src="EmptyDraw" alt="" srcset="">
        </article>

        <article 
          v-for="todo in todos_asc" 
          class="todo-item"
          :class="{
            done: todo.done,
            selected: isSelectedAll
          }"
        >
          <label>
            <input type="checkbox" v-model="todo.done">
            <span :class="`bubble ${ todo.category }`"></span>
          </label>

          <div class="todo-content">
            <input type="text" v-model="todo.content">
          </div>

          <div class="actions">
            <button 
              class="delete" 
              @click="removeTodo(todo)"
            >
              Delete
            </button>
          </div>
        </article>

        <article class="foot" v-show="!isEmpty">
          <div class="selection">
            <label class="select">
              <input type="checkbox" value="check-all" @click="isSelectedAll = !isSelectedAll && true">
              Select all
            </label>
          </div>
          
          <div class="actions" v-show="isSelectedAll == true">
            <button class="done" @click="markDone">              
              <span v-if="isAllDone">Unmark</span>
              <span v-else>Mark all as done</span>
              
            </button>
            <button class="delete" @click="deleteAll">Delete All</button>
          </div>
        </article>
      </div>
    </section>

  </main>
</template>