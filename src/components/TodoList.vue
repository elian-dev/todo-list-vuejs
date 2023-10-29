<script setup>
import { ref, computed } from "vue";
import EmptyDraw from "../assets/empty.svg";
import dragIcon from "../assets/drag-vertical-outline.svg";

const props = defineProps(["todos", "isEmpty"]);
const emit = defineEmits(["remove", "removeAll", "reorder"]);

const isSelectedAll = ref(false);
const isAllDone = ref(false);
const draggedItem = ref(null);

const removeTodo = (todo) => {
  const confirm = window.confirm(
    "Are you sure to delete this item? \nThis action can be reverted"
  );

  if (confirm) {
    emit("remove", todo);
  } else {
    return;
  }
};

const markDone = () => {
  props.todos.map((todo) => {
    todo.done = !isAllDone.value && true;
  });

  isAllDone.value = !isAllDone.value && true;
};

const deleteAll = () => {
  const confirm = window.confirm(
    "Are you sure to delete all items? \nThis action can be reverted"
  );

  if (confirm) {
    emit("removeAll");
  } else {
    return;
  }
};

// On drag start: store the item dragged
const handleDragStart = (index) => {
  draggedItem.value = index;
};

const handleDragOver = (event) => {
  event.preventDefault();
};

// On drop event: delete and reorder the state
const handleDrop = (index) => {
  const droppedItem = props.todos.splice(draggedItem.value, 1)[0];
  props.todos.splice(index, 0, droppedItem);

  emit("reorder", props.todos);

  draggedItem.value = null;
};

// On drag end: if drag end remove stored values
const handleDragEnd = () => {
  draggedItem.value = null;
};
</script>

<template>
  <section class="todo-list">
    <h3>TODO LIST</h3>
    <div class="list">
      <article
        v-for="(todo, index) in todos"
        class="todo-item"
        :draggable="true"
        @dragstart="handleDragStart(index)"
        @dragover="handleDragOver"
        @drop="handleDrop(index)"
        @dragend="handleDragEnd"
        :class="{
          done: todo.done,
          selected: isSelectedAll,
          dragged: index === draggedItem,
        }"
      >
        <img class="drag-icon" :src="dragIcon" alt="drag icon" />
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
      </article>

      <article class="foot" v-show="!props.isEmpty">
        <div class="selection">
          <label class="select">
            <input
              type="checkbox"
              name="check-all"
              value="check-all"
              @click="isSelectedAll = !isSelectedAll && true"
            />
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

  <article class="empty" v-if="isEmpty">
    <h2>Your task list is empty!</h2>
    <img :src="EmptyDraw" alt="Empty task list! Add a new todo" />
  </article>
</template>
