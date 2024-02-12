<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([]);
const name = ref('');

const input_content = ref('');
const input_category = ref(null);

const todo_asc = computed(() => todos.value.sort((a, b) => a.createdAt - b.createdAt));

watch(todos, newValue => {
  localStorage.setItem('todos', JSON.stringify(newValue));
}, { deep: true });

watch(name, (newValue) => {
  localStorage.setItem('name', newValue);
});

onMounted(() => {
  name.value = localStorage.getItem('name') || '';
  todos.value = JSON.parse(localStorage.getItem('todos')) || [];
});

const removeTodo = todo => {
  todos.value = todos.value.filter(t => t.id !== todo.id);
}

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }

  todos.value.push({
    id: Date.now(),
    content: input_content.value,
    catagorey: input_category.value,
    createdAt: new Date().getTime(),
    done: false
  });

  input_content.value = '';
  input_category.value = null;
}
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up <input type="text" placeholder="Name here" v-model="name" />
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>
      <form @submit.prevent="addTodo">
        <h4 class="title">What's on your mind?</h4>
        <input type="text" v-model="input_content" placeholder="e.g. Have lunch with mom" />
        
        <h4>What's the category of this task?</h4>
        <div class="options">
          <label>
            <input type="radio" name="category" value="standard" v-model="input_category" />
            <span class="bubble standard"></span> 
            <div>Standard</div>
          </label>
          <label>
            <input type="radio" name="category" value="urgent" v-model="input_category" />
            <span class="bubble urgent"></span> 
            <div>Urgent</div>
          </label>
          <input type="submit" value="Add todo">
        </div>
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LISTS</h3>
      <div v-for="todo in todo_asc" :class="`todo-item ${todo.done && 'done'}`">
        <label>
          <input type="checkbox" v-model="todo.done" />
          <span :class="`bubble ${todo.catagorey}`"></span>
        </label>
        <div class="todo-content">
          <input type="text" v-model="todo.content"/>
        </div>
        <div class="actions">
          <button class="delete" @click="removeTodo(todo)">Delete</button>
        </div>
      </div>
    </section>
  </main>
</template>
