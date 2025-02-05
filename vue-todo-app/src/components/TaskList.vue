<template>
  <div>
    <h2>Active Tasks</h2>
    <ul>
      <TaskItem
        v-for="task in activeTasks"
        :key="task.id"
        :task="task"
        @delete-task="forwardDelete"
        @update-task="forwardUpdate"
        @toggle-task-status="forwardToggle"
        @toggle-archive="forwardArchive"
      />
    </ul>
    <h2>Archived Tasks</h2>
    <ul>
      <TaskItem
        v-for="task in archivedTasks"
        :key="task.id"
        :task="task"
        @delete-task="forwardDelete"
        @update-task="forwardUpdate"
        @toggle-task-status="forwardToggle"
        @toggle-archive="forwardArchive"
      />
    </ul>
  </div>
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
  computed: {
    activeTasks() {
      return this.tasks.filter(task => !task.archived)
    },
    archivedTasks() {
      return this.tasks.filter(task => task.archived)
    }
  },
  methods: {
    forwardDelete(id) {
      this.$emit('delete-task', id)
    },
    forwardUpdate(payload) {
      this.$emit('update-task', payload)
    },
    forwardToggle(id) {
      this.$emit('toggle-task-status', id)
    },
    forwardArchive(id) {
      this.$emit('toggle-archive', id)
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
