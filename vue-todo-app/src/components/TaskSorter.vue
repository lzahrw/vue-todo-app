<!-- src/components/TaskSorter.vue -->
<template>
    <div class="task-sorter mb-2">
      <div class="row g-2 align-items-end">
        <div class="col-auto">
          <label for="deadlineSort" class="form-label mb-0">Sort by Deadline</label>
          <select v-model="deadlineSort" id="deadlineSort" class="form-select">
            <option value="none">None</option>
            <option value="asc">Ascending</option>
            <option value="desc">Descending</option>
          </select>
        </div>
        <div class="col-auto">
          <label for="prioritySort" class="form-label mb-0">Sort by Priority</label>
          <select v-model="prioritySort" id="prioritySort" class="form-select">
            <option value="none">None</option>
            <option value="lowToHigh">Low to High</option>
            <option value="highToLow">High to Low</option>
          </select>
        </div>
        <div class="col-auto">
          <button class="btn btn-primary" @click="sortTasks">Sort Tasks</button>
        </div>
        <div class="col-auto">
          <button class="btn btn-secondary" @click="hideSorter">Hide Sorter</button>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "TaskSorter",
    data() {
      return {
        deadlineSort: "none",
        prioritySort: "none",
      };
    },
    methods: {
      sortTasks() {
        // Emit the selected sort options
        this.$emit("sort-tasks", {
          deadlineSort: this.deadlineSort,
          prioritySort: this.prioritySort,
        });
      },
      hideSorter() {
        // Emit a cancel event so the parent can hide the sorter
        this.$emit("cancel-sort");
      },
    },
  };
  </script>
  
  <style scoped>
  /* Optional: Add any styling you need for the sorter */
  </style>
  