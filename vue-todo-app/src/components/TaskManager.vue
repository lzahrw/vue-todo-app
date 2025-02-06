<template>
  <!-- This component can use a scoped slot to pass the task state and operations to its child -->
  <slot 
    :tasks="tasks" 
    :addTask="addTask" 
    :deleteTask="deleteTask"
    :updateTask="updateTask" 
    :toggleTaskStatus="toggleTaskStatus"
    :updateTaskStatus="updateTaskStatus"
    :toggleArchive="toggleArchive">
  </slot>
</template>

<script>
import { ref, onMounted } from 'vue'

export default {
  name: 'TaskManager',
  setup() {
    // Retrieve saved tasks or initialize with an empty array.
    const tasks = ref(JSON.parse(localStorage.getItem('tasks')) || [])
    const nextId = ref(tasks.value.length > 0 ? tasks.value[tasks.value.length - 1].id + 1 : 1)

    // Save tasks to local storage.
    const saveTasksToLocalStorage = () => {
      localStorage.setItem('tasks', JSON.stringify(tasks.value))
    }

    // Add a new task with an initial status of "Pending".
    const addTask = (task) => {
      tasks.value.push({
        id: nextId.value++,
        title: task.title,
        status: 'Pending',
        priority: task.priority,
        dueDate: task.dueDate,
        archived: false,
      })
      saveTasksToLocalStorage()
    }

    // Delete a task by id.
    const deleteTask = (id) => {
      tasks.value = tasks.value.filter(task => task.id !== id)
      saveTasksToLocalStorage()
    }

    // Update task details (title, priority, due date).
    const updateTask = ({ id, title, priority, dueDate }) => {
      const task = tasks.value.find(task => task.id === id)
      if (task) {
        task.title = title
        task.priority = priority
        task.dueDate = dueDate
      }
      saveTasksToLocalStorage()
    }

    // Toggle task status using a cyclic order.
    // (You can remove this if you prefer to use updateTaskStatus exclusively.)
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

    // Update a task's status directly. This is useful for drag and drop.
    const updateTaskStatus = (updatedTask) => {
      const task = tasks.value.find(task => task.id === updatedTask.id)
      if (task) {
        task.status = updatedTask.status
      }
      saveTasksToLocalStorage()
    }

    // Toggle the archived state of a task.
    const toggleArchive = (id) => {
      const task = tasks.value.find(task => task.id === id)
      if (task) {
        task.archived = !task.archived
      }
      saveTasksToLocalStorage()
    }



    // Initialize tasks in localStorage if not already present.
    onMounted(() => {
      if (!localStorage.getItem('tasks')) {
        saveTasksToLocalStorage()
      }
    })

    return {
      tasks,
      addTask,
      deleteTask,
      updateTask,
      toggleTaskStatus,
      updateTaskStatus,
      toggleArchive,
    }
  }
}
</script>
