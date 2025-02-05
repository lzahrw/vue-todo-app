<template>
  <form @submit.prevent="handleSubmit">
    <input
      type="text"
      v-model="newTask.title"
      placeholder="Add a new task..."
    />
    <select v-model="newTask.priority">
      <option value="Low">Low</option>
      <option value="Medium">Medium</option>
      <option value="High">High</option>
    </select>
    <input
      type="date"
      v-model="newTask.dueDate"
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
    const newTask = ref({
      title: '',
      priority: 'Low',
      dueDate: '',
    })

    const handleSubmit = () => {
      if (newTask.value.title.trim() !== '') {
        emit('add-task', { ...newTask.value })
        newTask.value = { title: '', priority: 'Low', dueDate: '' }
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

input, select {
  padding: 0.5rem;
  margin-right: 0.5rem;
}

button {
  padding: 0.5rem 1rem;
}
</style>
