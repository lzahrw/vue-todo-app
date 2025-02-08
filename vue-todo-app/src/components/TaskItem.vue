<!-- src/components/TaskItem.vue -->
<template>
    <div class="task-item card mb-2">
      <div class="card-body p-2">
        <!-- Normal view when not editing -->
        <div v-if="!isEditing">
          <h5 class="card-title">{{ task.title }}</h5>
          <p class="card-text">
            Due: {{ task.dueDate || "No due date" }}<br />
            Priority: {{ task.priority }}
          </p>
          <div class="d-flex justify-content-between">
            <button class="btn btn-warning btn-sm" @click="startEditing">Edit</button>
            <button class="btn btn-danger btn-sm" @click="deleteTask">Delete</button>
            <button class="btn btn-secondary btn-sm" @click="archiveTask">Archive</button>
          </div>
        </div>
  
        <!-- Editing view -->
        <div v-else>
          <input
            type="text"
            v-model="editData.title"
            class="form-control mb-2"
            placeholder="Task title"
          />
          <input
            type="date"
            v-model="editData.dueDate"
            class="form-control mb-2"
          />
          <select v-model="editData.priority" class="form-select mb-2">
            <option value="Low">Low</option>
            <option value="Medium">Medium</option>
            <option value="High">High</option>
          </select>
          <!-- New: Board selection dropdown -->
          <select v-model="editData.boardId" class="form-select mb-2">
            <option 
              v-for="b in allBoards" 
              :value="b.id" 
              :key="b.id">
              {{ b.name }}
            </option>
          </select>
          <div class="d-flex justify-content-end">
            <button class="btn btn-success btn-sm" @click="saveEdit">Save</button>
            <button class="btn btn-secondary btn-sm ms-2" @click="cancelEdit">Cancel</button>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "TaskItem",
    props: {
      task: {
        type: Object,
        required: true
      },
      // Receive the list of all boards so we can show board names
      allBoards: {
        type: Array,
        required: true
      }
    },
    data() {
      return {
        isEditing: false,
        // Initialize editData with the current task’s details
        editData: {
          title: this.task.title,
          dueDate: this.task.dueDate,
          priority: this.task.priority,
          boardId: this.task.boardId // current board
        }
      };
    },
    methods: {
      startEditing() {
        // Copy current task data into editData and enter edit mode
        this.editData = {
          title: this.task.title,
          dueDate: this.task.dueDate,
          priority: this.task.priority,
          boardId: this.task.boardId
        };
        this.isEditing = true;
      },
      saveEdit() {
        // Construct updated task object
        const updatedTask = {
          ...this.task,
          title: this.editData.title,
          dueDate: this.editData.dueDate,
          priority: this.editData.priority,
          boardId: this.editData.boardId
        };
        // Emit the updated task so the parent can handle moving it if needed
        this.$emit("update-task", updatedTask);
        this.isEditing = false;
      },
      cancelEdit() {
        this.isEditing = false;
        // Reset editData to original values
        this.editData = {
          title: this.task.title,
          dueDate: this.task.dueDate,
          priority: this.task.priority,
          boardId: this.task.boardId
        };
      },
      deleteTask() {
        this.$emit("delete-task", this.task.id);
      },
      archiveTask() {
        this.$emit("archive-task", this.task.id);
      }
    },
    watch: {
      // If the task prop changes from the parent, update editData when not editing.
      task(newVal) {
        if (!this.isEditing) {
          this.editData = {
            title: newVal.title,
            dueDate: newVal.dueDate,
            priority: newVal.priority,
            boardId: newVal.boardId
          };
        }
      }
    }
  };
  </script>
  
  <style scoped>
  .task-item {
    cursor: move;
  }
  </style>
  