<script setup>
    import { ref, onMounted, watch } from 'vue'
    
    const name = ref('');

    watch(name, (newVal) => {
        localStorage.setItem('name', newVal)
    });

    onMounted(() => {
        name.value = localStorage.getItem('name') || '';
    });
</script>

<template>
    <section class="greeting">
      <h1 class="title">
        What's up, 
        <input type="text" placeholder="Name here" v-model="name">
      </h1>
    </section>
</template>
