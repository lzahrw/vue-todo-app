<!-- TaskBoard.vue -->
<template>
    <div class="task-board">
      <div
        class="task-column"
        v-for="status in statuses"
        :key="status"
        @dragover.prevent
        @drop="handleDrop(status)"
      >
        <h2>{{ status }}</h2>
        <ul>
          <li
            v-for="task in tasksByStatus(status)"
            :key="task.id"
            draggable="true"
            @dragstart="handleDragStart(task)"
          >
            <!-- You can include your TaskItem component here -->
            <TaskItem
              :task="task"
              @delete-task="deleteTask"
              @update-task="updateTask"
              @toggle-archive="toggleArchive"
            />
          </li>
        </ul>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from 'vue'
  import TaskItem from './TaskItem.vue'
  
  export default {
    name: 'TaskBoard',
    components: { TaskItem },
    props: {
      tasks: {
        type: Array,
        required: true,
      },
    },
    emits: ['update-task-status', 'delete-task', 'update-task', 'toggle-archive'],
    setup(props, { emit }) {
      // Define the three statuses for the columns.
      const statuses = ['Pending', 'In Progress', 'Completed']
  
      // Reference to the task currently being dragged.
      const draggedTask = ref(null)
  
      // When a drag starts, record the task.
      const handleDragStart = (task) => {
        draggedTask.value = task
      }
  
      // When a task is dropped into a column, update its status.
      const handleDrop = (newStatus) => {
        if (draggedTask.value) {
          // Create an updated task object with the new status.
          const updatedTask = { ...draggedTask.value, status: newStatus }
          // Emit an event so the parent (TaskManager) can update the task.
          emit('update-task-status', updatedTask)
          // Clear the dragged task.
          draggedTask.value = null
        }
      }
  
      // Return the tasks that belong in a given column.
      const tasksByStatus = (status) => {
        return props.tasks.filter(
          (task) => task.status === status && !task.archived
        )
      }
  
      // Forward delete, update, and archive events if needed.
      const deleteTask = (id) => emit('delete-task', id)
      const updateTask = (payload) => emit('update-task', payload)
      const toggleArchive = (id) => emit('toggle-archive', id)
  
      return {
        statuses,
        draggedTask,
        handleDragStart,
        handleDrop,
        tasksByStatus,
        deleteTask,
        updateTask,
        toggleArchive,
      }
    },
  }
  </script>
  
  <style scoped>
  .task-board {
    display: flex;
    justify-content: space-between;
    gap: 1rem;
  }
  
  .task-column {
    flex: 1;
    padding: 10px;
    border: 2px dashed #ccc;
    min-height: 300px;
  }
  
  .task-column h2 {
    text-align: center;
  }
  </style>
  