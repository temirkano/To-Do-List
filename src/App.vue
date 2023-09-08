<template>
  <div id="app">
    <div class="app-content">
        <div class="app-content--title">
          <h1>To-Do List</h1>
          <img src="@/assets/todo.png" alt="icon todo">
        </div>
        <TodoList 
          v-bind:todos="todos"
        />
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import TodoList from "@/components/TodoList.vue";
import axios from "axios"

export interface Todo {
  id: number
  title : string ,
  completed: boolean,
  checked: boolean;
}

export default defineComponent({
  name: "App",
  data() {
    return {
      todos: [] as Todo[],
      newTodo: '' as string,
    }
  },
  methods: {
    async fetchTodos() {
      try {
        const response = await axios.get('https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts');
        this.todos = response.data;
        this.saveTodosToLocalStorage();
      } catch (error) {
        console.error('Ошибка при загрузке данных:', error);
      }
    },
    addTodo() {
      if (this.newTodo && this.newTodo.trim() !== "") {
    this.todos.push({
    title: this.newTodo, completed: false,
    id: this.todos.length + 1,
    checked: false
});
    this.saveTodosToLocalStorage();
    this.newTodo = '';
  }
  },
  saveTodosToLocalStorage() {
      localStorage.setItem("todos", JSON.stringify(this.todos));
    },
},
  components: {
    TodoList
  },
  mounted() {


    const savedTodos = localStorage.getItem("todos");
    if (savedTodos) {
      this.todos = JSON.parse(savedTodos);
    }


    this.fetchTodos();
  },
});
</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  display: flex;
  flex-direction: column;
  justify-content: center;
  .app-content{
    width: 100%;
    height: fit-content;
    background: #ffffff;
    .app-content--title{
    display: flex;
    justify-content: center;
    padding-right: 14%;
    margin-top: 13%;
      h1 {
        margin: 0;
        margin-bottom: 15px;
      }

      img {
        padding-left: 10px;
        width: 30px;
        height: 30px;
      }
    }
  }
}
</style>
