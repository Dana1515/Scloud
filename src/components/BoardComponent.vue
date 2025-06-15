<template>
  <section class="taskList"> 
    <h1 class="main-title board">Доска задач</h1> 

    <div class="boards">

      <div class="board">
        <div class="board-name">Открыто</div>
        <div class="boardzone" @dragover.prevent @drop="onDrop">
          <div class="board-task-name" v-for="task in openTasks" :key="task.id">
            <div
              class="task-name"
              draggable="true"
              @dragstart="onDragStart(task.id, $event)"
              @dragend="onDragEnd"
            >
              {{ task.text }}
            </div>
          </div>
        </div>
      </div>

      <div class="board">
        <div class="board-name">В работе</div>
        <div class="boardzone" @dragover.prevent @drop="onDrop">
          <div class="board-task-name" v-for="task in inProgressTasks" :key="task.id">
            <div
              class="task-name"
              draggable="true"
              @dragstart="onDragStart(task.id, $event)"
              @dragend="onDragEnd"
            >
              {{ task.text }}
            </div>
          </div>
        </div>
      </div>

      <div class="board">
        <div class="board-name">Закрыто</div>
        <div class="boardzone" @dragover.prevent @drop="onDrop">
          <div class="board-task-name" v-for="task in finishedTasks" :key="task.id">
            <div
              class="task-name"
              draggable="true"
              @dragstart="onDragStart(task.id, $event)"
              @dragend="onDragEnd"
            >
              {{ task.text }}
            </div>
          </div>
        </div>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  name: 'BoardComponent',
  props: {
    tasks: {
      type: Array,
      required: true
    }
  },

  data() {
    return {
      localTasks: [] 
    };
  },

  watch: {
    tasks: {
      immediate: true,
      handler(newTasks) {
        this.localTasks = [...newTasks]; 
      }
    }
  },

  computed: {
    openTasks() {
      return this.localTasks.filter(task => task.status === 'Открыт');
    },
    inProgressTasks() {
      return this.localTasks.filter(task => task.status === 'В работе');
    },
    finishedTasks() {
      return this.localTasks.filter(task => task.status === 'Закрыт');
    }
  },

  methods: {
    onDragStart(taskId, event) {
      event.dataTransfer.setData('text/plain', taskId);
      event.target.style.opacity = 0.5;
    },

    onDragEnd(event) {
      event.target.style.opacity = '';
    },

    onDrop(event) {
      const taskId = event.dataTransfer.getData('text/plain');
      const task = this.localTasks.find(task => task.id == taskId);

      if (!task) return;

      const boardName = event.target.closest('.board').querySelector('.board-name').innerText;

      if (boardName === 'Открыто') {
        task.status = 'Открыт';
      } else if (boardName === 'В работе') {
        task.status = 'В работе';
      } else if (boardName === 'Закрыто') {
        task.status = 'Закрыт';
      }

      this.$emit('update-tasks', [...this.localTasks]);
    }
  }
};
</script>

<style scoped>
@import '../assets/css/main.css';
@import '../assets/css/taskList.css';
</style>
