<script setup>
    import { ref, computed } from 'vue';
    import EmptyDraw from '../assets/empty.svg';
    
    const props = defineProps(['todos', 'isEmpty']);
    const emit = defineEmits(['remove', 'removeAll']);

    const isSelectedAll = ref(false);
    const isAllDone = ref(false);

    const removeTodo = todo => {
        const confirm = window.confirm("Are you sure to delete this item? \nThis action can be reverted");

        if(confirm) {
          emit('remove', todo);
        } else {
            return;
        }
    }

    const markDone = () => {
        props.todos.map((todo) => {
            todo.done = !isAllDone.value && true;
        });

        isAllDone.value = !isAllDone.value && true;
    }

    const deleteAll = () => {
        const confirm = window.confirm("Are you sure to delete all items? \nThis action can be reverted");
        
        if(confirm) {
            emit('removeAll')
        } else {
            return;
        }
    }

    const todos_asc = computed(() => props.todos.sort((a, b) => {
        return b.createdAt - a.createdAt;
    }));

</script>

<template>
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

        <article class="foot" v-show="!props.isEmpty">
          <div class="selection">
            <label class="select">
              <input type="checkbox" name="check-all" value="check-all" @click="isSelectedAll = !isSelectedAll && true">
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
</template>