<template>
  <TaskManager v-slot="taskManager">
    <div id="app">
      <h1>Task Management</h1>
      <button @click="toggleNewTaskForm">
        {{ showNewTaskForm ? 'Cancel' : 'New Task' }}
      </button>

      <TaskForm
        v-if="showNewTaskForm"
        @add-task="taskManager.addTask"
      />
      <button @click="toggleArchivedTasksForm">
        {{ showArchivedTasksForm ? 'Cancel' : 'Archived Tasks' }}
      </button>

      <!-- Use TaskBoard instead of TaskList -->
      <TaskBoard
        :tasks="taskManager.tasks"
        @delete-task="taskManager.deleteTask"
        @update-task="taskManager.updateTask"
        @update-task-status="taskManager.updateTaskStatus"
        @toggle-archive="taskManager.toggleArchive"
      />

      
      <ul v-if="showArchivedTasksForm" >
        <h2>Archived Tasks</h2>
        <!-- Filter the tasks array for archived tasks -->
        <TaskItem
          v-for="task in taskManager.tasks.filter(t => t.archived)"
          :key="task.id"
          :task="task"
          @delete-task="taskManager.deleteTask"
          @update-task="taskManager.updateTask"
          @toggle-task-status="taskManager.toggleTaskStatus"
          @toggle-archive="taskManager.toggleArchive"
        />
      </ul>
    </div>
  </TaskManager>
</template>


<script>
import { ref } from 'vue'
import TaskManager from './components/TaskManager.vue'
import TaskForm from './components/TaskForm.vue'
import TaskBoard from './components/TaskBoard.vue'  // Import the new component
import TaskItem from './components/TaskItem.vue'

export default {
  name: 'App',
  components: {
    TaskManager,
    TaskForm,
    TaskBoard,
    TaskItem,
  },


  setup() {
    const showNewTaskForm = ref(false)
    const showArchivedTasksForm = ref(false)

    const toggleNewTaskForm = () => {
      showNewTaskForm.value = !showNewTaskForm.value
    }
    const toggleArchivedTasksForm = () => {
      showArchivedTasksForm.value = !showArchivedTasksForm.value
    }
    
    return {
      showNewTaskForm,
      showArchivedTasksForm,
      toggleNewTaskForm,
      toggleArchivedTasksForm,
    }
  }
}
</script>
