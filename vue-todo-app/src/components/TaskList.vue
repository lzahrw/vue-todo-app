<!-- src/components/TaskList.vue -->
<template>
    <div>
      <draggable
        :list="board.tasks"
        :group="{ name: 'tasks', pull: true, put: true }"
        @end="onTaskDrop"
        item-key="id"
        class="task-list mb-3"
      >
        <template #item="{ element }">
          <TaskItem
            :task="element"
            :allBoards="allBoards"
            @edit-task="handleEditTask"
            @delete-task="handleDeleteTask"
            @archive-task="handleArchiveTask"
            @update-task="handleUpdateTask"
          />
        </template>
      </draggable>
  
      <!-- Button to show the add task form -->
      <div v-if="!showTaskForm" class="mt-2">
        <button class="btn btn-primary btn-sm" @click="showTaskForm = true">
          Add New Task
        </button>
      </div>
  
      <!-- Add Task Form -->
      <div v-if="showTaskForm" class="mt-2">
        <input
          v-model="newTaskTitle"
          type="text"
          class="form-control"
          placeholder="Task title"
        />
        <input
          v-model="newTaskDueDate"
          type="date"
          class="form-control mt-1"
        />
        <select v-model="newTaskPriority" class="form-select mt-1">
          <option value="Low">Low</option>
          <option value="Medium" selected>Medium</option>
          <option value="High">High</option>
        </select>
        <div class="mt-2">
          <button class="btn btn-success btn-sm" @click="addTask">
            Confirm
          </button>
          <button class="btn btn-secondary btn-sm ms-2" @click="cancelTask">
            Cancel
          </button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  import { ref } from "vue";
  import draggable from "vuedraggable";
  import TaskItem from "./TaskItem.vue"; // Import the TaskItem component
  
  export default {
    name: "TaskList",
    components: {
      draggable,
      TaskItem // Register TaskItem here
    },
    props: {
      board: {
        type: Object,
        required: true
      },
      // allBoards is passed if you later need to move a task between boards
      allBoards: {
        type: Array,
        required: true
      }
    },
    emits: ["add-task", "move-task", "delete-task", "archive-task", "update-tasks"],
    setup(props, { emit }) {
      // Show/hide add task form
      const showTaskForm = ref(false);
      // New task fields
      const newTaskTitle = ref("");
      const newTaskDueDate = ref("");
      const newTaskPriority = ref("Medium");
  
      // Add a new task to the current board
      const addTask = () => {
        if (newTaskTitle.value.trim() === "") return;
        const newTask = {
          id: "task-" + Date.now(),
          title: newTaskTitle.value.trim(),
          dueDate: newTaskDueDate.value,
          priority: newTaskPriority.value,
          boardId: props.board.id
        };
        emit("add-task", props.board.id, newTask);
        // Reset the form
        newTaskTitle.value = "";
        newTaskDueDate.value = "";
        newTaskPriority.value = "Medium";
        showTaskForm.value = false;
      };
  
      const cancelTask = () => {
        newTaskTitle.value = "";
        newTaskDueDate.value = "";
        newTaskPriority.value = "Medium";
        showTaskForm.value = false;
      };
  
      // Handle updated task from TaskItem without mutating the board prop
      const handleUpdateTask = (updatedTask) => {
        if (updatedTask.boardId !== props.board.id) {
          // If the task’s board changed, emit an event to move the task to the correct board.
          emit("move-task", updatedTask);
        } else {
          // Create a new tasks array with the updated task and emit it for the parent to update.
          const updatedTasks = props.board.tasks.map(task =>
            task.id === updatedTask.id ? updatedTask : task
          );
          emit("update-tasks", props.board.id, updatedTasks);
        }
      };
  
      // Re-emit TaskItem events upward
      const handleDeleteTask = (taskId) => {
        emit("delete-task", props.board.id, taskId);
      };
  
      const handleArchiveTask = (taskId) => {
        emit("archive-task", props.board.id, taskId);
      };
  
      // When a task is dropped, update its boardId (if moved between boards)
      const onTaskDrop = (evt) => {
        // Create a new tasks array where each task's boardId is updated:
        const updatedTasks = props.board.tasks.map(task => ({
          ...task,
          boardId: props.board.id
        }));
        // Emit an event to the parent so it can update the board's tasks:
        emit("update-tasks", props.board.id, updatedTasks);
        console.log("Task dropped:", evt);
      };
  
      return {
        showTaskForm,
        newTaskTitle,
        newTaskDueDate,
        newTaskPriority,
        addTask,
        cancelTask,
        onTaskDrop,
        handleDeleteTask,
        handleArchiveTask,
        handleUpdateTask
      };
    }
  };
  </script>
  
  <style scoped>
  .task-list {
    min-height: 50px;

    padding: 5px;
    border-radius: 4px;
  }
  </style>
  