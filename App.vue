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
  