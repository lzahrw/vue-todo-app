<template>
    <li :class="taskClass">
      <div v-if="isEditing">
        <!-- The TaskEdit component -->
        <TaskEdit
          :task="task"
          @update-task="updateTask"
          @close-edit="closeEdit"
        />
      </div>
      <div v-else>
        <span :class="priorityClass">{{ task.title }}</span>
        <span :class="priorityClass">{{ task.priority }}</span>
        <span :class="dueDateClass">{{ formattedDueDate }}</span>
        <button @click="startEditing">Edit</button>
        <button @click="deleteTask">Delete</button>
        <button @click="toggleArchive">{{ task.archived ? 'Unarchive' : 'Archive' }}</button>
        <button @click="changeStatus">{{ task.status }}</button>
      </div>
    </li>
  </template>
  
  <script>
  import { ref, computed } from 'vue'
  import TaskEdit from './TaskEdit.vue'
  
  export default {
    name: 'TaskItem',
    components: { TaskEdit },
    props: {
      task: {
        type: Object,
        required: true
      },
    },
    emits: ['delete-task', 'update-task', 'toggle-task-status', 'toggle-archive'],
    setup(props, { emit }) {
      const isEditing = ref(false)
  
      const startEditing = () => {
        isEditing.value = true
      }
  
      const closeEdit = () => {
        isEditing.value = false
      }
  
      const updateTask = (updatedTask) => {
        emit('update-task', updatedTask)
        isEditing.value = false // Close the modal after saving
      }
  
      const deleteTask = () => {
        emit('delete-task', props.task.id)
      }
  
      const toggleStatus = () => {
        emit('toggle-task-status', props.task.id)
      }
  
      const toggleArchive = () => {
        emit('toggle-archive', props.task.id)
      }
  
      const changeStatus = () => {
        const statusOrder = ['Pending', 'In Progress', 'Completed']
        const currentStatusIndex = statusOrder.indexOf(props.task.status)
        const nextStatusIndex = (currentStatusIndex + 1) % statusOrder.length
        const newStatus = statusOrder[nextStatusIndex]
        emit('toggle-task-status', props.task.id)
      }
  
      // Computed class for styling task state
      const taskClass = computed(() => ({
        completed: props.task.status === 'Completed',
        inProgress: props.task.status === 'In Progress',
        pending: props.task.status === 'Pending',
      }))
  
      const priorityClass = computed(() => {
        if (props.task.priority === 'High') return 'high-priority'
        if (props.task.priority === 'Medium') return 'medium-priority'
        return 'low-priority'
      })
  
      const dueDateClass = computed(() => {
        const now = new Date()
        const dueDate = new Date(props.task.dueDate)
        if (dueDate < now) return 'overdue'
        if (dueDate - now <= 24 * 60 * 60 * 1000) return 'due-soon'
        return ''
      })
  
      const formattedDueDate = computed(() => {
        if (!props.task.dueDate) return ''
        return new Date(props.task.dueDate).toLocaleDateString()
      })
  
      return {
        isEditing,
        startEditing,
        closeEdit,
        updateTask,
        deleteTask,
        toggleStatus,
        toggleArchive,
        changeStatus,
        taskClass,
        priorityClass,
        dueDateClass,
        formattedDueDate
      }
    }
  }
  </script>
  
  <style scoped>
  .completed {
    text-decoration: line-through;
  }
  
  .inProgress {
    background-color: lightblue;
  }
  
  .pending {
    background-color: lightgray;
  }
  
  .high-priority {
    color: red;
  }
  
  .medium-priority {
    color: orange;
  }
  
  .low-priority {
    color: green;
  }
  
  .due-soon {
    background-color: yellow;
  }
  
  .overdue {
    background-color: red;
  }
  </style>
  