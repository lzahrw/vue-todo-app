<template>
    <form @submit.prevent="handleSubmit">
      <input
        type="text"
        v-model="newTask"
        placeholder="Add a new task..."
      />
      <button type="submit">Add Task</button>
    </form>
  </template>
  
  <script>
  import { ref } from 'vue'
  export default {
    name: 'TaskForm',
    emits: ['add-task'],
    setup(props, { emit }) {
      const newTask = ref('')
  
      const handleSubmit = () => {
        if (newTask.value.trim() !== '') {
          emit('add-task', newTask.value)
          newTask.value = ''
        }
      }
  
      return {
        newTask,
        handleSubmit,
      }
    }
  }
  </script>
  
  <style scoped>
  form {
    display: flex;
    margin-bottom: 1rem;
  }
  
  input {
    flex: 1;
    padding: 0.5rem;
    margin-right: 0.5rem;
  }
  
  button {
    padding: 0.5rem 1rem;
  }
  </style>
  