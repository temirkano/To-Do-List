<template>
  <div>
    <li>
      <span class="box">
        <input type="checkbox" v-model="checked" class="custom-checkbox">
        <p :class="{ title: isTurbid, titleCrossed: checked }" @click="turbid">{{ todo.title }}</p>
      </span>
      <img src="../assets/cross.svg" alt="icon cross" class="cross" @click="removeTodo">
    </li>
  </div>
</template>
  
<script lang="ts">
  import { ref, watch } from 'vue';
  import axios from 'axios';
  import {Todo} from '../App.vue'

interface ITodoItemProps {
  todo: Todo;
  onRemove: (todoId: number) => void;
}

  export default {
    props: {
      todo: {
        type: Object as () => Todo,
        required: true,
      },
      onRemove: {
        type: Function as unknown as () => ITodoItemProps["onRemove"],
        required: true,
      },
    },
    setup(props) {
      const localStorageKey = `checked_${props.todo.id}`;
      const checked = ref(false);
      const isTurbid = ref(true);

      function saveCheckedToLocalStorage(checkedValue: boolean) {
  localStorage.setItem(localStorageKey, JSON.stringify(checkedValue));
}

      watch(checked, (newChecked) => {
    saveCheckedToLocalStorage(newChecked);
  });

  const savedChecked = localStorage.getItem(localStorageKey);
  if (savedChecked !== null) {
    checked.value = JSON.parse(savedChecked);
  }

      function turbid() {
        isTurbid.value = !isTurbid.value;
      }
      
      async function removeTodo() {
        try {
          const response = await axios.delete(`https://64a53a0600c3559aa9bf50d1.mockapi.io/Posts/${props.todo.id}`);
          if (response.status === 200) {
            props.onRemove(props.todo.id);
            window.location.reload();
          } else {
            throw new Error('Ошибка при удалении задачи из MockAPI');
          }
        } catch (error) {
          console.error('Ошибка при удалении задачи:', error);
        }
      }
        
      return {
        checked,
        isTurbid,
        turbid,
        removeTodo,
      };
    },
  };
</script>
  
<style scoped lang="scss">
  li {
    width: 31%;
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin: auto;
    color: rgb(87, 88, 88);
    .box{
    display: flex;
    align-items: center;
    .custom-checkbox {
      appearance: none;
      -webkit-appearance: none;
      -moz-appearance: none;
      width: 20px;
      height: 20px;
      background-color: #fff;
      border: 2px solid rgb(205, 206, 206);
      cursor: pointer;
      border-radius: 50px;
      margin-right: 10px;
    }
    .custom-checkbox:checked {
      background-color: red;
      border-color: red;
    }
  }
    .cross {
      width: 30px;
      height: 30px;
      cursor: pointer;
    }
    .title {
      color: rgb(87, 88, 88);
    }
    .titleCrossed {
      text-decoration: line-through;
    }
  }
</style>
    