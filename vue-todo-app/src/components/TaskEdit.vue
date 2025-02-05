<template>
    <div class="modal">
      <div class="modal-content">
        <h2>Edit Task</h2>
        <input type="text" v-model="editTitle" />
        <button @click="submitEdit">Save</button>
        <button @click="$emit('close')">Cancel</button>
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
        required: true,
      },
    },
    emits: ['update-task', 'close'],
    setup(props, { emit }) {
      const editTitle = ref(props.task.title)
  
      watch(
        () => props.task.title,
        (newTitle) => {
          editTitle.value = newTitle
        }
      )
  
      const submitEdit = () => {
        if (editTitle.value.trim() !== '') {
          emit('update-task', { id: props.task.id, title: editTitle.value })
        }
        emit('close')
      }
  
      return { editTitle, submitEdit }
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
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .modal-content {
    background: white;
    padding: 1rem;
    border-radius: 5px;
  }
  </style>
  