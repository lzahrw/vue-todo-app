<!-- src/components/BoardList.vue -->
<template>
    <div class="container my-3">
      <h2>Boards</h2>
      <div class="d-flex mb-3">
      <!-- Button to show input field for adding a new board -->
      <button
        v-if="!showInput"
        class="btn btn-primary"
        @click="showInput = true"
      >
        Add New Board
      </button>
      <!-- Button to open the Archived Tasks modal -->
      <button class="btn btn-secondary ms-2" @click="showArchiveModal = true">
        View Archived Tasks
      </button>
    </div>
      <!-- (Buttons for adding board and viewing archived tasks remain unchanged) -->
      <!-- ... -->

          <!-- Input field for board name -->
    <div v-if="showInput" class="mt-3">
      <input
        v-model="newBoardName"
        type="text"
        class="form-control"
        placeholder="Enter board name"
        @keyup.enter="addBoard"
      />
      <button class="btn btn-success mt-2" @click="addBoard">
        Confirm
      </button>
      <button class="btn btn-secondary mt-2 ms-2" @click="cancelAddBoard">
        Cancel
      </button>
    </div>
  
      <!-- Draggable container for boards -->
      <draggable
        :list="boards"
        group="boards"
        @end="onBoardDrop"
        class="row"
        item-key="id"
      >
        <template #item="{ element }">
          <div class="col-md-4 mb-3">
            <div class="card">
              <!-- Board header with three-dot dropdown -->
              <div class="card-header bg-primary text-white d-flex justify-content-between align-items-center">
                <span>{{ element.name }}</span>
                <div class="dropdown">
                  <button
                    class="btn btn-link text-white dropdown-toggle"
                    type="button"
                    :id="'boardOptions' + element.id"
                    data-bs-toggle="dropdown"
                    aria-expanded="false"
                  >
                    ⋮
                  </button>
                  <ul
                    class="dropdown-menu dropdown-menu-end"
                    :aria-labelledby="'boardOptions' + element.id"
                  >
                    <li>
                      <!-- Open the sorter when Sort Tasks is clicked -->
                      <a class="dropdown-item" href="#" @click.prevent="openSorter(element.id)">Sort Tasks</a>
                    </li>
                    <li>
                      <a class="dropdown-item text-danger" href="#" @click.prevent="deleteBoard(element.id)">Delete Board</a>
                    </li>
                  </ul>
                </div>
              </div>
              <div class="card-body">
                <!-- Display TaskSorter if this board is in sorting mode -->
                <TaskSorter
                  v-if="sortingBoardId === element.id"
                  @sort-tasks="handleSortTasks(element.id, $event)"
                  @cancel-sort="cancelSort"
                />
                <!-- Display the TaskList for this board -->
                <TaskList
                  :board="element"
                  :allBoards="boards"
                  @add-task="handleAddTask"
                  @move-task="handleMoveTask"
                  @delete-task="handleDeleteTask"
                  @archive-task="handleArchiveTask"
                  @update-tasks="handleUpdateTasks"
                />
              </div>
            </div>
          </div>
        </template>
      </draggable>
  
      <!-- Archived Tasks Modal remains unchanged -->
      <ArchivedTasksModal
        v-if="showArchiveModal"
        :archivedTasks="archivedTasks"
        @close="showArchiveModal = false"
        @restore-task="restoreArchivedTask"
        @delete-task="deleteArchivedTask"
      />
    </div>
  </template>
  
  <script>
  import { ref, watch, onMounted } from "vue";
  import draggable from "vuedraggable";
  import TaskList from "./TaskList.vue";
  import ArchivedTasksModal from "./ArchivedTasksModal.vue";
  import TaskSorter from "./TaskSorter.vue";
  
  export default {
    name: "BoardList",
    components: { draggable, TaskList, ArchivedTasksModal, TaskSorter },
    setup() {
      const boards = ref([]);
      const showInput = ref(false);
      const newBoardName = ref("");
      const archivedTasks = ref([]);
      const showArchiveModal = ref(false);
      // Tracks which board's sorter is visible (null means none)
      const sortingBoardId = ref(null);
  
      onMounted(() => {
        const storedBoards = localStorage.getItem("boards");
        if (storedBoards) {
          boards.value = JSON.parse(storedBoards);
        }
        const storedArchivedTasks = localStorage.getItem("archivedTasks");
        if (storedArchivedTasks) {
          archivedTasks.value = JSON.parse(storedArchivedTasks);
        }
      });
  
      watch(boards, (newVal) => {
        localStorage.setItem("boards", JSON.stringify(newVal));
      }, { deep: true });
  
      watch(archivedTasks, (newVal) => {
        localStorage.setItem("archivedTasks", JSON.stringify(newVal));
      }, { deep: true });
  
      const saveBoards = () => {
        localStorage.setItem("boards", JSON.stringify(boards.value));
      };
  
      // Board operations (add, delete, etc.)
      const addBoard = () => {
        if (newBoardName.value.trim() !== "") {
          boards.value.push({
            id: "board-" + Date.now(),
            name: newBoardName.value.trim(),
            tasks: [],
          });
          newBoardName.value = "";
          showInput.value = false;
          saveBoards();
        }
      };
  
      const deleteBoard = (boardId) => {
        boards.value = boards.value.filter((board) => board.id !== boardId);
        saveBoards();
      };
  
      const cancelAddBoard = () => {
        newBoardName.value = "";
        showInput.value = false;
      };
  
      const onBoardDrop = (evt) => {
        console.log("Board dropped:", evt);
        saveBoards();
      };
  
      // Task event handlers (unchanged)
      const handleAddTask = (boardId, newTask) => {
        const board = boards.value.find((b) => b.id === boardId);
        if (board) {
          board.tasks.push(newTask);
          saveBoards();
        }
      };
  
      const handleMoveTask = (updatedTask) => {
        boards.value.forEach((board) => {
          board.tasks = board.tasks.filter((t) => t.id !== updatedTask.id);
        });
        const targetBoard = boards.value.find((b) => b.id === updatedTask.boardId);
        if (targetBoard) {
          targetBoard.tasks.push(updatedTask);
          saveBoards();
        }
      };
  
      const handleDeleteTask = (boardId, taskId) => {
        const board = boards.value.find((b) => b.id === boardId);
        if (board) {
          board.tasks = board.tasks.filter((t) => t.id !== taskId);
          saveBoards();
        }
      };
  
      const handleArchiveTask = (boardId, taskId) => {
        const board = boards.value.find((b) => b.id === boardId);
        if (board) {
          const idx = board.tasks.findIndex((t) => t.id === taskId);
          if (idx !== -1) {
            const archivedTask = board.tasks.splice(idx, 1)[0];
            archivedTask.archivedAt = Date.now();
            archivedTasks.value.push(archivedTask);
            saveBoards();
          }
        }
      };
  
      const handleUpdateTasks = (boardId, updatedTasks) => {
        const board = boards.value.find((b) => b.id === boardId);
        if (board) {
          board.tasks = updatedTasks;
          saveBoards();
        }
      };
  
      // Open the sorter for a specific board
      const openSorter = (boardId) => {
        sortingBoardId.value = boardId;
      };
  
      // Handle sorting options from TaskSorter
      const handleSortTasks = (boardId, sortOptions) => {
        const board = boards.value.find((b) => b.id === boardId);
        if (!board) return;
        board.tasks.sort((a, b) => {
          let cmp = 0;
          if (sortOptions.deadlineSort !== "none") {
            const dateA = a.dueDate ? new Date(a.dueDate) : new Date(8640000000000000);
            const dateB = b.dueDate ? new Date(b.dueDate) : new Date(8640000000000000);
            cmp = sortOptions.deadlineSort === "asc" ? dateA - dateB : dateB - dateA;
            if (cmp !== 0) return cmp;
          }
          if (sortOptions.prioritySort !== "none") {
            const priorityMap = { Low: 1, Medium: 2, High: 3 };
            const prioA = priorityMap[a.priority] || 0;
            const prioB = priorityMap[b.priority] || 0;
            cmp = sortOptions.prioritySort === "lowToHigh" ? prioA - prioB : prioB - prioA;
          }
          return cmp;
        });
        saveBoards();
        // Hide the sorter after sorting
        sortingBoardId.value = null;
      };
  
      // Hide the sorter when the cancel event is emitted
      const cancelSort = () => {
        sortingBoardId.value = null;
      };
  
      // Archived task handlers (unchanged)
      const restoreArchivedTask = (task) => {
        archivedTasks.value = archivedTasks.value.filter((t) => t.id !== task.id);
        if (task.archivedAt) delete task.archivedAt;
        const board = boards.value.find((b) => b.id === task.boardId);
        if (board) {
          board.tasks.push(task);
        } else {
          boards.value.push({
            id: task.boardId,
            name: "Restored Board",
            tasks: [task],
          });
        }
        saveBoards();
      };
  
      const deleteArchivedTask = (task) => {
        archivedTasks.value = archivedTasks.value.filter((t) => t.id !== task.id);
      };
  
      return {
        boards,
        showInput,
        newBoardName,
        addBoard,
        deleteBoard,
        cancelAddBoard,
        onBoardDrop,
        handleAddTask,
        handleMoveTask,
        handleDeleteTask,
        handleArchiveTask,
        handleUpdateTasks,
        archivedTasks,
        showArchiveModal,
        restoreArchivedTask,
        deleteArchivedTask,
        sortingBoardId,
        openSorter,
        handleSortTasks,
        cancelSort,
      };
    },
  };
  </script>
  