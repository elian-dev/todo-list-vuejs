<script setup>
import { ref } from "vue";

const props = defineProps(["todos"]);

const input_content = ref("");
const input_category = ref(null);

const addTodo = () => {
  if (input_content.value.trim() === "" || input_category.value === null) {
    return;
  }

  props.todos.push({
    content: input_content.value.trim(),
    category: input_category.value,
    done: false,
    createdAt: new Date().getTime(),
  });

  input_content.value = "";
  input_category.value = null;
};
</script>

<template>
  <section class="create-todo">
    <h3>CREATE A TODO</h3>

    <form class="form" @submit.prevent="addTodo">
      <h4>What's on your todo list?</h4>

      <input
        type="text"
        placeholder="e.g. Make a video"
        v-model="input_content"
        name="todo-name"
        id="todo-name"
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

      <input type="submit" name="create-todo" value="Add todo" />
    </form>
  </section>
</template>
