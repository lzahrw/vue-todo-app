<template>
  <div class="modal">
    <div class="modal-content">
      <h2>Edit Task</h2>
      <form @submit.prevent="saveEdit">
        <input
          v-model="taskData.title"
          type="text"
          placeholder="Task Title"
        />
        <select v-model="taskData.priority">
          <option value="Low">Low</option>
          <option value="Medium">Medium</option>
          <option value="High">High</option>
        </select>
        <input
          v-model="taskData.dueDate"
          type="date"
        />
        <div class="buttons">
          <button type="submit">Save</button>
          <button type="button" @click="cancelEdit">Cancel</button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
import { ref, watch } from 'vue'

export default {
  name: 'TaskEdit',
  props: {
    task: {
      type: Object,
      required: true
    },
  },
  emits: ['update-task', 'close-edit'],
  setup(props, { emit }) {
    const taskData = ref({
      ...props.task,
    })

    // Watch for any task changes to update local data
    watch(() => props.task, (newTask) => {
      taskData.value = { ...newTask }
    })

    const saveEdit = () => {
      emit('update-task', taskData.value) // Emit updated task
      emit('close-edit') // Close the modal after saving
    }

    const cancelEdit = () => {
      emit('close-edit') // Close without saving
    }

    return {
      taskData,
      saveEdit,
      cancelEdit,
    }
  }
}
</script>

<style scoped>
.modal {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
}

.modal-content {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  width: 400px;
}

input, select {
  width: 100%;
  padding: 8px;
  margin: 10px 0;
}

button {
  padding: 8px 16px;
  margin-right: 10px;
}

.buttons {
  display: flex;
  justify-content: flex-end;
}
</style>
