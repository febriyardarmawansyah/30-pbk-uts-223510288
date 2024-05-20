<script setup>
import { ref, onMounted, computed, watch } from 'vue';

const props = defineProps({
  initialTodos: {
    type: Array,
    required: true
  },
  initialName: {
    type: String,
    default: ''
  }
});

const todos = ref(props.initialTodos);
const name = ref(props.initialName);

const input_content = ref('');
const input_category = ref(null);

const todos_asc = computed(() => todos.value.sort((a, b) => {
  return a.createdAt - b.createdAt;
}));

watch(name, (newVal) => {
  localStorage.setItem('name', newVal);
});

watch(todos, (newVal) => {
  localStorage.setItem('todos', JSON.stringify(newVal));
}, {
  deep: true
});

const addTodo = () => {
  if (input_content.value.trim() === '' || input_category.value === null) {
    return;
  }

  todos.value.push({
    content: input_content.value,
    category: input_category.value,
    done: false,
    editable: false,
    createdAt: new Date().getTime()
  });
  input_content.value = '';
  input_category.value = null;
};

const removeTodo = (todo) => {
  todos.value = todos.value.filter((t) => t !== todo);
};

onMounted(() => {
  name.value = localStorage.getItem('name') || props.initialName;
  todos.value = JSON.parse(localStorage.getItem('todos')) || props.initialTodos;
});
</script>

<template>
  <main class="app">
    <section class="greeting">
      <h2 class="title">
        What's up, <input type="text" id="name" placeholder="Name here" v-model="name">
      </h2>
    </section>

    <section class="create-todo">
      <h3>CREATE A TODO</h3>

      <form id="new-todo-form" @submit.prevent="addTodo">
        <h4>New Task!</h4>
        <input 
          type="text" 
          name="content" 
          id="content" 
          placeholder="type your task here"
          v-model="input_content" />
        
        <h4>Pick a category</h4>
        <div class="options">

          <label>
            <input 
              type="radio" 
              name="category" 
              id="category1" 
              value="personal"
              v-model="input_category" />
            <span class="bubble personal"></span>
            <div>Personal</div>
          </label>

          <label>
            <input 
              type="radio" 
              name="category" 
              id="category2" 
              value="college"
              v-model="input_category" />
            <span class="bubble college"></span>
            <div>College</div>
          </label>

        </div>

        <input type="submit" value="Add todo" />
      </form>
    </section>

    <section class="todo-list">
      <h3>TODO LIST</h3>
      <div class="list" id="todo-list">
        <slot name="todo-item" v-for="todo in todos_asc" :todo="todo" :removeTodo="removeTodo" :key="todo.createdAt">
          <div :class="['todo-item', { done: todo.done }]">
            <label>
              <input type="checkbox" v-model="todo.done" />
              <span :class="`bubble ${todo.category === 'personal' ? 'personal' : 'college'}`"></span>
            </label>

            <div class="todo-content">
              <input type="text" v-model="todo.content" />
            </div>

            <div class="actions">
              <button class="delete" @click="removeTodo(todo)">Delete</button>
            </div>
          </div>
        </slot>
      </div>
    </section>
  </main>
</template>

<style scoped>
.app {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.greeting .title input {
  border: none;
  border-bottom: 1px solid #000;
}

.create-todo {
  margin-top: 20px;
  width: 100%;
  max-width: 500px;
}

.create-todo .options {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}

.create-todo .options label {
  display: flex;
  align-items: center;
  cursor: pointer;
}

.create-todo .options .bubble {
  display: inline-block;
  width: 20px;
  height: 20px;
  border-radius: 50%;
  margin-right: 10px;
}

.create-todo .options .bubble.personal {
  background-color: blue;
}

.create-todo .options .bubble.college {
  background-color: green;
}

.todo-list {
  margin-top: 20px;
  width: 100%;
  max-width: 500px;
}

.todo-list .list {
  display: flex;
  flex-direction: column;
}

.todo-item {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding: 10px;
  border-bottom: 1px solid #ccc;
}

.todo-item.done .todo-content input {
  text-decoration: line-through;
}

.todo-content input {
  border: none;
  background: none;
}
</style>
