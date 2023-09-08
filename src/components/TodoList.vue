<template>
  <div>
    <ul>
      <div class="input-container">
        <input v-model="newTodo" @keyup.enter="addNewTodo" placeholder="Add your task">
        <button @click="addNewTodo">Add</button>
      </div>
      <TodoItem
        v-for="todo in todos"
        :key="todo.id"
        :todo="todo"
        :onRemove="removeTodo"
      />
    </ul>
  </div>
</template>
  
<script lang="ts">
  import { Todo } from '@/App.vue';
  import TodoItem from '../components/TodoItem.vue';
  import axios from "axios"
  
  export default {
    data() {
      return {
        newTodo: '',
        todos: [] as Todo[],
      }
    },
    components: {
      TodoItem
    },
    methods: {
    async fetchTodosFromMockAPI() {
      try {
        const response = await fetch("https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts");
        if (!response.ok) {
          throw new Error("Ошибка при загрузке данных с MockAPI");
        }
        const data = await response.json();
        this.todos = data;
      } catch (error) {
        console.error("Ошибка при загрузке данных:", error);
      }
    },

    async addTodo() {
      if (this.newTodo.trim() === "") return;
      try {
        const response = await fetch("https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts", {
          method: "POST",
          headers: {
            "Content-Type": "application/json",
          },
          body: JSON.stringify({ title: this.newTodo }),
        });

        if (!response.ok) {
          throw new Error("Ошибка при добавлении новой задачи в MockAPI");
        }

        const data = await response.json();
  
        if (typeof data === 'object' && data !== null) {
          this.todos.push(data as Todo);
        } else {
          console.error('"data" имеет неправильный тип');
        }
        this.newTodo = "";
        this.saveTodosToLocal();
      } catch (error) {
        console.error("Ошибка при добавлении новой задачи:", error);
      }
    },

    async addNewTodo() {
        if (this.newTodo.trim() === "") return;
        await this.addTodo();
    },

    async removeTodo(todoId: number) {
      try {
        const response = await axios.delete(`https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts/${todoId}`);
        if (response.status === 200) {
          this.todos = this.todos.filter(todo => todo.id !== todoId);
          this.saveTodosToLocal();
        } else {
          throw new Error('Ошибка при удалении задачи из MockAPI');
        }
      } catch (error) {
        console.error('Ошибка при удалении задачи:', error);
      }
    },

    saveTodosToLocal() {
      localStorage.setItem('todos', JSON.stringify(this.todos));
    },

    fetchTodosFromLocal() {
      const storedTodos = localStorage.getItem('todos');
        if (storedTodos) {
          this.todos = JSON.parse(storedTodos);
        }
    },

    async fetchTodos() {
      try {
        const response = await axios.get('https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts');
        const data = response.data;
        this.todos = data;
        this.saveTodosToLocal();
      } catch (error) {
        console.error('Ошибка при загрузке данных:', error);
      }
    }
  },

  created() {
      this.fetchTodosFromLocal();
      this.fetchTodosFromMockAPI();
    }
  }
</script>
  
<style lang="scss">
ul {
  margin: 0;
}
  .input-container {
  display: flex;
  align-items: center;
  position: relative;
  width: 30%;
  margin-bottom: 20px;
  margin: auto;
}
.input-container input {
  flex: 1;
  border-radius: 50px;
  padding-right: 30px;
  padding: 10px 15px;
  height: 35px;
  border: none;
  outline: none;
  background: rgb(231, 235, 235);
}

.input-container button {
  position: absolute; 
  right: 0.1px;
  top: 50%;
  border-radius: 50px;
  cursor: pointer;
  width: 120px;
  height: 55px;
  padding: 5px;
  transform: translateY(-50%);
  background: red;
  color: white;
  border: none;
}
</style>