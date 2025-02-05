<template>
  <div id="app">
    <h1>Task Management</h1>
    <button @click="toggleNewTaskForm">
      {{ showNewTaskForm ? 'Cancel' : 'New Task' }}
    </button>

    <!-- Only show TaskForm when showNewTaskForm is true -->
    <TaskForm v-if="showNewTaskForm" @add-task="addTask" />

    <TaskList
      :tasks="tasks"
      @delete-task="deleteTask"
      @update-task="updateTask"
      @toggle-task-status="toggleTaskStatus"
      @toggle-archive="toggleArchive"
    />

  </div>
</template>

<script>
import { ref, onMounted } from 'vue'
import TaskForm from './components/TaskForm.vue'
import TaskList from './components/TaskList.vue'


export default {
  name: 'App',
  components: {
    TaskForm,
    TaskList,

  },
  setup() {
    const showNewTaskForm = ref(false)
    const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])


    // Dynamically calculate the next ID based on the existing tasks
    const nextId = ref(tasks.value.length > 0 ? tasks.value[tasks.value.length - 1].id + 1 : 1)



    // Save tasks to localStorage
    const saveTasksToLocalStorage = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    }

    // Toggle visibility of the new task form
    const toggleNewTaskForm = () => {
      showNewTaskForm.value = !showNewTaskForm.value
    }

    // Add a new task
    const addTask = (task) => {
      tasks.value.push({
        id: nextId.value++,  // Increment nextId
        title: task.title,
        status: 'Pending', // Default status
        priority: task.priority,
        dueDate: task.dueDate,
        archived: false,
      })
      saveTasksToLocalStorage()
      showNewTaskForm.value = false

    }

    // Delete a task by id
    const deleteTask = (id) => {
      tasks.value = tasks.value.filter(task => task.id !== id)
      saveTasksToLocalStorage()
    }

    // Update a task by id
    const updateTask = ({ id, title, priority, dueDate }) => {
      const task = tasks.value.find(task => task.id === id)
      if (task) {
        task.title = title
        task.priority = priority
        task.dueDate = dueDate
      }
      saveTasksToLocalStorage()
    }

    // Toggle task status (Pending, In Progress, Completed)
    const toggleTaskStatus = (id) => {
      const task = tasks.value.find(task => task.id === id)
      if (task) {
        const statusOrder = ['Pending', 'In Progress', 'Completed']
        const currentIndex = statusOrder.indexOf(task.status)
        const nextIndex = (currentIndex + 1) % statusOrder.length
        task.status = statusOrder[nextIndex]
      }
      saveTasksToLocalStorage()
    }


    // Toggle task archived status
    const toggleArchive = (id) => {
      const task = tasks.value.find(task => task.id === id)
      if (task) {
        task.archived = !task.archived
      }
      saveTasksToLocalStorage()
    }

    // Initialize tasks if none exist in localStorage
    onMounted(() => {
      if (!localStorage.getItem('tasks')) {
        saveTasksToLocalStorage() // Initialize with empty task list if not found
      }
    })

    return {
      tasks,
      addTask,
      deleteTask,
      updateTask,
      toggleTaskStatus,
      toggleArchive,
      showNewTaskForm,
      toggleNewTaskForm,
    }
  }
}
</script>

<style scoped>
#app {
  max-width: 800px;
  margin: 0 auto;
  padding: 1rem;
  font-family: Arial, sans-serif;
}

button {
  padding: 0.5rem 1rem;
  margin-bottom: 1rem;
  cursor: pointer;
}
</style>


