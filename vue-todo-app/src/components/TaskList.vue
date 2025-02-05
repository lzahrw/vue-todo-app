<template>
    <ul>
      <TaskItem
        v-for="task in tasks"
        :key="task.id"
        :task="task"
        @delete-task="forwardDelete"
        @update-task="forwardUpdate"
        @toggle-task-status="forwardToggle"
      />
    </ul>
  </template>
  
  <script>
  import TaskItem from './TaskItem.vue'
  export default {
    name: 'TaskList',
    components: { TaskItem },
    props: {
      tasks: {
        type: Array,
        required: true,
      },
    },
    emits: ['delete-task', 'update-task', 'toggle-task-status'],
    methods: {
      forwardDelete(id) {
        this.$emit('delete-task', id)
      },
      forwardUpdate(payload) {
        this.$emit('update-task', payload)
      },
      forwardToggle(id) {
        this.$emit('toggle-task-status', id)
      }
    }
  }
  </script>
  
  <style scoped>
  ul {
    list-style: none;
    padding: 0;
  }
  </style>
  