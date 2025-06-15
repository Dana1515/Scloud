<!-- ToDoListComponent.vue -->
<template>
    <TableComponent @update-tasks="handleTasksUpdate" />
    <BoardComponent :tasks="tasksFromTable" />

</template>

<script>
import TableComponent from './components/TableComponent.vue';
import BoardComponent from './components/BoardComponent.vue';

export default {
  name: 'ToDoListComponent',
  components: {
    TableComponent,
    BoardComponent
  },
  data() {
    return {
      tasksFromTable: []
    };
  },
  methods: {
    handleTasksUpdate(updatedTasks) {
      this.tasksFromTable = updatedTasks;
      console.log('Получены задачи из TableComponent:', this.tasksFromTable);
    }
  }
};
</script>



<style scoped>
@import 'assets/css/main.css';
@import 'assets/css/toDoList.css';
@import 'assets/css/table.css';
@import 'assets/css/taskList.css'; 
</style>