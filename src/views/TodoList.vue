<template>
  <div class="container">
    <h1>This is an TodoList</h1>
    <router-link to="/">back Home</router-link>
    <hr>

    <input v-model="title" @keyup.enter="addTodo" type="text" class="todo-input">
    <hr>
    <Loader v-if="loading"/>
    <ul v-else-if="filteredTodos.length" class="todo-list">
      <li v-for="(todo, index) in filteredTodos" :key="todo.id" class="todo-item">
        <span :class="{done: todo.completed}">
          <input type="checkbox" @change="todo.completed = !todo.completed" class="todo-checkbox">
          <strong class="todo-index">{{index + 1}}</strong>
          {{todo.title | uppercase}}
        </span>
        <button @click="removeItem" class="btn-rm">&times;</button>
      </li>
    </ul>
    <p v-else>No Todos</p>
    <hr>
    <div class="filter">
      <select v-model="filter">
        <option value="all">All</option>
        <option value="completed">Completed</option>
        <option value="not-completed">Not-completed</option>
      </select>
    </div>
  </div>
</template>

<script>
import Loader from '@/components/Loader'
export default {
  data() {
    return {
      todos: [],
      title: "",
      loading: true,
      filter: 'all'
    }
  },
  filters: {
    uppercase(value) {
      return value.toUpperCase()
    }
  },
  components: {
    Loader,
  },
  mounted () {
    fetch('https://jsonplaceholder.typicode.com/todos/?_limit=3')
      .then(response => response.json())
      .then(json => {
        setTimeout(() => {
          this.todos = json
          this.loading = false
        }, 500);
      })
  },
  methods: {
    addTodo() {
      const newTodo = {
        id: Date.now(),
        title: this.title,
        completed: false
      }
      this.todos.push(newTodo)
      this.title = ''
    },
    removeItem(index) {
      this.todos.splice(index, 1)
    }
  },
  computed: {
    filteredTodos() {
      if(this.filter === 'all') {
        return this.todos 
      }
      if(this.filter === 'completed') {
        return this.todos.filter(t => t.completed) 
      }
      if(this.filter === 'not-completed') {
        return this.todos.filter(t => !t.completed) 
      }
    }
  },
}
</script>

<style>
  .todo-input {
    margin-left: 20px;
    width: 600px;
  }

  .todo-list {
    list-style: none;
  }

  .todo-checkbox, .todo-index {
    margin-left: 5px;
  }

  .todo-item {
    display: flex;
    justify-content: space-between;
    width: 600px;
    border: 1px solid black;
    align-items: center;
    margin-bottom: 10px;
  }

  .btn-rm {
    color: red;
  }

  .done {
    text-decoration: line-through;
  }

  .filter {
    display: flex;
    justify-content: center;
  }
</style>
