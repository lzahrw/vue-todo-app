<template>
    <li :class="{ completed: task.completed }">
      <!-- Inline editing: show input when editing, otherwise show the title -->
      <div v-if="isEditing">
        <input v-model="editTitle" @keyup.enter="saveEdit" @blur="saveEdit" />
      </div>
      <div v-else>
        <span @click="toggleStatus">{{ task.title }}</span>
        <button @click="startEditing">Edit</button>
        <button @click="deleteTask">Delete</button>
      </div>
    </li>
  </template>
  
  <script>
  import { ref, watch } from 'vue'
  export default {
    name: 'TaskItem',
    props: {
      task: {
        type: Object,
        required: true,
      },
    },
    emits: ['delete-task', 'update-task', 'toggle-task-status'],
    setup(props, { emit }) {
      const isEditing = ref(false)
      const editTitle = ref(props.task.title)
  
      // Watch for changes in the task prop to update the local edit field.
      watch(
        () => props.task.title,
        (newTitle) => {
          editTitle.value = newTitle
        }
      )
  
      const startEditing = () => {
        isEditing.value = true
        // Optionally, focus the input after next tick.
      }
  
      const saveEdit = () => {
        if (editTitle.value.trim() === '') {
          // If empty, you might want to cancel the edit or revert to the original title.
          editTitle.value = props.task.title
        } else {
          emit('update-task', { id: props.task.id, title: editTitle.value })
        }
        isEditing.value = false
      }
  
      const deleteTask = () => {
        emit('delete-task', props.task.id)
      }
  
      const toggleStatus = () => {
        emit('toggle-task-status', props.task.id)
      }
  
      return {
        isEditing,
        editTitle,
        startEditing,
        saveEdit,
        deleteTask,
        toggleStatus,
      }
    }
  }
  </script>
  
  <style scoped>
  li {
    display: flex;
    align-items: center;
    justify-content: space-between;
    padding: 0.5rem;
    border-bottom: 1px solid #ddd;
  }
  
  .completed span {
    text-decoration: line-through;
    color: gray;
  }
  
  span {
    flex: 1;
    cursor: pointer;
  }
  
  button {
    margin-left: 0.5rem;
  }
  </style>
  