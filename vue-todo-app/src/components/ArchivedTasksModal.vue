<!-- src/components/ArchivedTasksModal.vue -->
<template>
    <div class="modal fade show d-block" tabindex="-1" role="dialog">
      <div class="modal-dialog modal-lg" role="document">
        <div class="modal-content">
          <div class="modal-header">
            <h5 class="modal-title">Archived Tasks</h5>
            <!-- Close button -->
            <button type="button" class="btn-close" aria-label="Close" @click="$emit('close')"></button>
          </div>
          <div class="modal-body">
            <div v-if="!archivedTasks || archivedTasks.length === 0">
              <p>No archived tasks found.</p>
            </div>
            <div v-else class="row">
              <div
                v-for="task in archivedTasks"
                :key="task.id"
                class="col-md-4 mb-3"
              >
                <div class="card">
                  <div class="card-body">
                    <h5 class="card-title">{{ task.title }}</h5>
                    <p class="card-text">
                      Archived on: {{ new Date(task.archivedAt).toLocaleDateString() }}
                    </p>
                    <button
                      class="btn btn-success btn-sm"
                      @click="$emit('restore-task', task)"
                    >
                      Restore
                    </button>
                    <button
                      class="btn btn-danger btn-sm ms-2"
                      @click="$emit('delete-task', task)"
                    >
                      Delete Permanently
                    </button>
                  </div>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </template>
  
  <script>
  export default {
    name: "ArchivedTasksModal",
    props: {
      archivedTasks: {
        type: Array,
        default: () => [],
      },
    },
  };
  </script>
  
  <style scoped>
  /* A simple backdrop effect */
  .modal {
    background: rgba(0, 0, 0, 0.5);
  }
  </style>
  