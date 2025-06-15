<template>
    <div v-if="isVisible" class="task-popup">
    <div class="popup-overlay">
      <div class="card-content">
        <div class="card-block">
          <h1 class="title-change-task">Изменение задачи</h1>
          <button class="btn-popup-close" @click="$emit('close')">
            <img src="../assets/images/popup-close.png" />
          </button>
        </div>
  
        <input type="text" class="popup-input" v-model="localTask.text" 
        minlength="5" 
        maxlength="25" 
        required  />
  
        <hr>
  
        <div class="block-status">
          <div class="block-status" v-if="localTask.status === 'Открыт'">
            <button class="btn-status" @click="moveToInProgress" :class="{ activeColor: isInProgressActive }" >В работу</button>
            <button class="btn-status" @click="closeTask"  :class="{ activeColor: isClosedActive }" >Закрыть</button>
          </div>

          <div class="block-status" v-if="localTask.status === 'В работе'">
            <button class="btn-status" @click="doLater" :class="{ activeColor: isInDoLaterActive }" >Отложить</button>
            <button class="btn-status" @click="closeTask" :class="{ activeColor: isClosedActive  }" >Закрыть</button>
          </div>

          <div class="block-status" v-if="localTask.status === 'Закрыт'">
            <button class="btn-status" @click="reOpen" :class="{ activeColor: isInReOpenActive }">Переоткрыть</button>
          </div>
        </div>
  
        <div class="btns-card">
          <button class="btn-popup-apply" @click="applyChanges">Применить</button>
          <button class="btn-popup-delete" @click="$emit('delete')">Удалить задачу</button>
        </div>
      </div>

    </div>
      
    </div>
  
</template>
  
  <script>
  export default {
    props: {
      task: Object,
      isVisible: Boolean,
    },
  
    data() {
      return {
        originalStatus: '',
        tempStatus: '',
        isInProgressActive: false,
        isClosedActive: false,
        isInDoLaterActive:false,
        isInReOpenActive: false,
        localTask: { 
          ...this.task, 
          status: localStorage.getItem('taskStatus') || this.task.status 
        },
      
      };
    },

    mounted() {
    this.originalStatus = this.localTask.status;
},
  
    methods: {
        moveToInProgress(){
          this.tempStatus = this.isInProgressActive ? '' : 'В работе';
            this.isInProgressActive = !this.isInProgressActive;
            if (this.isInProgressActive) {
                this.isClosedActive = false; 
            }
        },
        closeTask(){
          this.isClosedActive = !this.isClosedActive; 
            this.tempStatus = this.isClosedActive ? 'Закрыт' : ''; 
            this.isInProgressActive = false; 
            this.isInDoLaterActive = false; 
           
        },
        doLater(){
          this.isInDoLaterActive = !this.isInDoLaterActive;
          this.tempStatus = this.isInDoLaterActive ? 'Открыт' : '';
          this.isClosedActive = false; 
          this.isInProgressActive = false; 
           
        },
        reOpen(){
            this.isInReOpenActive = !this.isInReOpenActive;
            this.tempStatus = this.isInReOpenActive ? 'Открыт' : '';
            
        },
        applyChanges(){
          if (this.localTask.text.length < 5 || this.localTask.text.length > 25) {
            alert("Длина задачи должна быть от 5 до 25 символов.");
            return;
          }
           this.localTask.status = this.tempStatus; 
           if (this.localTask.status=='') {
                this.localTask.status = this.originalStatus; 
            }
           console.log('Обновленный статус:', this.localTask.status); 
           console.log('Оригинальный статус:', this.originalStatus); 
           this.$emit('apply', this.localTask); 
           
        },
    },
  };
  </script>
    
  
<style >

@import '../assets/css/taskPopup.css'; 
 

</style>