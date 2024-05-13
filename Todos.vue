<<<<<<< HEAD:src/Todos.vue
<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				List Tugas Kuliah<input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3></h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4></h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder=""
					v-model="input_content" />
				
				<h4></h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Individu</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Team</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3></h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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
=======
<<<<<<<< HEAD:src/App.vue
<template>
	<div class="container">
	  <header>
		<nav>
		  <ul>
			<li @click="selectedMenu = 'Post'" :class="{ active: selectedMenu === 'Post' }">Post</li>
			<li @click="selectedMenu = 'Todos'" :class="{ active: selectedMenu === 'Todos' }">Todos</li>
		  </ul>
		</nav>
	  </header>
	  <div class="content">
		<div v-if="selectedMenu === 'Post'" class="post-section">
		  <select v-model="selectedUser" @change="fetchPosts">
			<option v-for="user in users" :key="user.id" :value="user.id">{{ user.name }}</option>
		  </select>
		  <div v-if="postsLoading">
			<p>Loading...</p>
		  </div>
		  <div v-else>
			<h2>Postingan User: {{ selectedUserName }}</h2>
			<ul>
			  <li v-for="post in posts" :key="post.id">
				<h3>{{ post.title }}</h3>
				<p>{{ post.body }}</p>
			  </li>
			</ul>
		  </div>
		</div>
		<div v-else-if="selectedMenu === 'Todos'" class="todos-section">
		  <transition name="fade">
			<div key="todos-section">
			  <Todos :todos="todos" />
			</div>
		  </transition>
		</div>
	  </div>
	</div>
  </template>
  
  <script>
  import Todos from './Todos.vue';
  
  export default {
	components: {
	  Todos
	},
	data() {
	  return {
		selectedMenu: 'Post',
		users: [],
		selectedUser: null,
		selectedUserName: '',
		posts: [],
		postsLoading: false, // Added loading state for posts
		todos: [] // Fill in with your fetched Todos data
	  };
	},
	methods: {
	  fetchUsers() {
		fetch('https://jsonplaceholder.typicode.com/users')
		  .then(response => response.json())
		  .then(data => {
			this.users = data;
		  })
		  .catch(error => {
			console.error('Error fetching users:', error);
		  });
	  },
	  fetchPosts() {
		if (!this.selectedUser) return;
		this.postsLoading = true; // Set loading state to true before fetching
		fetch(`https://jsonplaceholder.typicode.com/posts?userId=${this.selectedUser}`)
		  .then(response => response.json())
		  .then(data => {
			this.posts = data;
			this.postsLoading = false; // Set loading state to false after fetching
		  })
		  .catch(error => {
			console.error('Error fetching posts:', error);
			this.posts = []; // Clear posts if an error occurs
			this.postsLoading = false; // Set loading state to false after error
		  });
		const selectedUser = this.users.find(user => user.id === parseInt(this.selectedUser));
		if (selectedUser) {
		  this.selectedUserName = selectedUser.name;
		}
	  }
	},
	watch: {
	  selectedUser() {
		this.fetchPosts();
	  }
	},
	created() {
	  this.fetchUsers();
	}
  };
  </script>
  
  <style>
  .container {
	display: flex;
	flex-direction: column;
	justify-content: center;
	align-items: center;
	height: 100vh;
  }
  
  .content {
	max-width: 800px;
  }
  
  header {
	background-color: #333;
	padding: 10px 0;
  }
  
  nav ul {
	list-style: none;
	padding: 0;
	margin: 0;
  }
  
  nav ul li {
	display: inline-block;
	margin-right: 20px;
	color: #fff;
	cursor: pointer;
  }
  
  nav ul li.active {
	font-weight: bold;
  }
  
  .post-section,
  .todos-section {
	margin-top: 20px;
  }
  
  .fade-enter-active,
  .fade-leave-active {
	transition: opacity 0.5s;
  }
  
  .fade-enter,
  .fade-leave-to {
	opacity: 0;
  }
  </style>
  
========
<script setup>
import { ref, onMounted, computed, watch } from 'vue'

const todos = ref([])
const name = ref('')

const input_content = ref('')
const input_category = ref(null)

const todos_asc = computed(() => todos.value.sort((a,b) =>{
	return a.createdAt - b.createdAt
}))

watch(name, (newVal) => {
	localStorage.setItem('name', newVal)
})

watch(todos, (newVal) => {
	localStorage.setItem('todos', JSON.stringify(newVal))
}, {
	deep: true
})

const addTodo = () => {
	if (input_content.value.trim() === '' || input_category.value === null) {
		return
	}

	todos.value.push({
		content: input_content.value,
		category: input_category.value,
		done: false,
		editable: false,
		createdAt: new Date().getTime()
	})
}

const removeTodo = (todo) => {
	todos.value = todos.value.filter((t) => t !== todo)
}

onMounted(() => {
	name.value = localStorage.getItem('name') || ''
	todos.value = JSON.parse(localStorage.getItem('todos')) || []
})
</script>

<template>
	<main class="app">
		
		<section class="greeting">
			<h2 class="title">
				List Tugas Kuliah<input type="text" id="name" placeholder="Name here" v-model="name">
			</h2>
		</section>

		<section class="create-todo">
			<h3></h3>

			<form id="new-todo-form" @submit.prevent="addTodo">
				<h4></h4>
				<input 
					type="text" 
					name="content" 
					id="content" 
					placeholder=""
					v-model="input_content" />
				
				<h4></h4>
				<div class="options">

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category1" 
							value="business"
							v-model="input_category" />
						<span class="bubble business"></span>
						<div>Individu</div>
					</label>

					<label>
						<input 
							type="radio" 
							name="category" 
							id="category2" 
							value="personal"
							v-model="input_category" />
						<span class="bubble personal"></span>
						<div>Team</div>
					</label>

				</div>

				<input type="submit" value="Add todo" />
			</form>
		</section>

		<section class="todo-list">
			<h3></h3>
			<div class="list" id="todo-list">

				<div v-for="todo in todos_asc" :class="`todo-item ${todo.done && 'done'}`">
					<label>
						<input type="checkbox" v-model="todo.done" />
						<span :class="`bubble ${
							todo.category == 'business' 
								? 'business' 
								: 'personal'
						}`"></span>
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
>>>>>>>> 08df256ceb10074cb15db755a94d584583490bbc:Todos.vue
>>>>>>> 08df256ceb10074cb15db755a94d584583490bbc:Todos.vue
