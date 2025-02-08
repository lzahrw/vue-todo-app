<!-- src/App.vue -->
<template>
  <div class="app container">
    <h1 class="my-4">Task Manager</h1>
    <BoardList/>
  </div>
</template>

<script>
import BoardList from './components/BoardList.vue'

export default {
  name: 'App',
  components: { BoardList }
}
</script>

<style>
/* Global styles (if needed) */
</style>
