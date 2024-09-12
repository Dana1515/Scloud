<template>
    <section class="currentTasks"> 
        <div class="block tasks"> 
            <div class="block-current-tasks">  
                <h2 class="title-block">Текущие задачи</h2>  
                <div class="tasks">  
                    <div class="part tasks-begin"> 
                        <div class="content"> 
                            <img src="../assets/images/info.svg" alt="иконка"/> 
                            <h3 class="tasks-type">Открыто - {{ openTasksCount }} </h3> 
                        </div>  
                    </div>  
                
                    <div class="part tasks-current">  
                        <div class="content"> 
                            <img src="../assets/images/info-2.svg" alt="иконка"/> 
                            <h3 class="tasks-type">В работе - {{ inProgressTasksCount }} </h3> 
                        </div> 
                    </div>  
                
                    <div class="part tasks-finished">  
                        <div class="content"> 
                            <img src="../assets/images/info-3.svg" alt="иконка"/> 
                            <h3 class="tasks-type">Закрыто - {{ finishedTasksCount }} </h3> 
                        </div> 
                    </div>  
                </div>  
            </div> 
         
            <div class="block-current-tasks ">  
                <h2 class="title-block">Добавить задачу</h2>  
                <div class="content add">  
                    <button class="add-btn" @click="addTask"></button> 
                    <div class="input-container"> 
                        <input 
                            type="text" 
                            class="input-tasks" 
                            placeholder="Текст" 
                            v-model="newTask"
                            minlength="5" 
                            maxlength="25" 
                            required
                        > 
                        <button 
                            class="clear-btn" 
                            @click="clearInput()" 
                            aria-label="Очистить поле"
                        >
                            <img src="../assets/images/close.jpg"/>
                        </button> 
                    </div> 
                </div>  
            </div> 
        </div> 

        <div class="block tasks-table"> 
            <table> 
                <thead> 
                    <tr class="table-title"> 
                        <th class="task">Задачи</th> 
                        <th class="status">Статус</th> 
                    </tr> 
                </thead> 
                <tbody class="table-content"> 
                    <tr v-for="task in displayedTasks" :key="task.id"> 
                        <td class="task-text">{{ task.text }}</td> 
                        <td class="btn"> 
                            <button class="btn-status" @click="openPopup(task)">{{ task.status }}</button> 
                        </td> 
                    </tr> 
                </tbody> 
            </table> 

            <button class="btn-table-more" v-if="hasMoreTasks" @click="toggleTasks" :class="{ active: hasMoreTasks }"> 
                {{ allTasksVisible ? 'Скрыть' : 'Показать еще' }}  
                <span class="arrow"> 
                    <img src="../assets/images/arrow.svg"/> 
                </span> 
            </button> 
             
        </div> 
    </section>
    
    <section class="taskList"> 
        <h1 class="main-title board">Доска задач</h1> 
 
        <div class="boards">
            <div class="board">
                <div class="board-name">Открыто</div>
                <div class="boardzone" id="dropzone" @dragover.prevent @drop="onDrop"   >
                    <div  class=" board-task-name" v-for="task in openTasks" :key="task.id" > 
                        <div id="draggable" class="task-name " draggable="true" @dragstart="onDragStart" @dragend="onDragEnd" >{{ task.text }}</div>
                    </div>
                </div>
            </div>

            <div class="board">
                <div class="board-name">В работе</div>
                <div class="boardzone" id="dropzone" @dragover.prevent @drop="onDrop"  >
                    <div  class=" board-task-name"  v-for="task in  inProgressTasks" :key="task.id" >
                        <div id="draggable" class="task-name " draggable="true" @dragstart="onDragStart" @dragend="onDragEnd" >{{ task.text }}</div>
                    </div>
                </div>
            </div> 

            <div class="board">
                <div class="board-name">Закрыто</div>
                <div class="boardzone" id="dropzone" @dragover.prevent @drop="onDrop"  >
                    <div  class=" board-task-name"  v-for="task in  finishedTasks" :key="task.id">
                        <div id="draggable" class="task-name " draggable="true" @dragstart="onDragStart" @dragend="onDragEnd" >{{ task.text }}</div>
                    </div>
                </div>
            </div> 
        </div>
       
    </section> 

    <TaskPopup  
                v-if="isPopupVisible"  
                :task="selectedTask"  
                :isVisible="isPopupVisible"  
                @apply="handleApply"  
                @delete="handleDelete"  
                @close="closePopup"
                  
    /> 
</template>

<script>

import TaskPopup from './TaskPopup.vue';

export default {   
    name: 'ToDoList', 
    components:  { TaskPopup,},
            
    data() {   
        return {   

            allTasksVisible: false,
            visibleTasks: 5, 
            newTask: '',   
            tasks: this.getTasksFromLocalStorage(),  
            nextTaskId: this.getNextTaskIdFromLocalStorage(),
            isPopupVisible: false, 
            selectedTask: null, 
        };   
    },   
    computed: { 
        openTasksCount() { 
            return this.tasks.filter(task => task.status === 'Открыт').length; 
        }, 
        openTasks() {  
            return this.tasks.filter(task => task.status === 'Открыт');  
        },
        inProgressTasksCount() { 
            return this.tasks.filter(task => task.status === 'В работе').length; 
        }, 
        inProgressTasks() {  
            return this.tasks.filter(task => task.status === 'В работе');  
        },
        finishedTasksCount() { 
            return this.tasks.filter(task => task.status === 'Закрыт').length; 
        }, 
        finishedTasks() {  
            return this.tasks.filter(task => task.status === 'Закрыт');  
        },
        displayedTasks() {
            return this.allTasksVisible ? this.tasks : this.tasks.slice(0, this.visibleTasks);
        },
        hasMoreTasks() {
            return this.tasks.length > this.visibleTasks;
        },
    }, 
    methods: { 
        onDragStart(event) {
      event.dataTransfer.setData('text/plain', event.target.id);
      event.target.style.opacity = 0.5; 
    },
    onDragEnd(event) {
      event.target.style.opacity = ''; 
    },
    onDrop(event) {
      const data = event.dataTransfer.getData('text/plain');
      const draggableElement = document.getElementById(data);
      event.target.appendChild(draggableElement); 
      const taskId = event.dataTransfer.getData('text/plain'); 
        const task = this.tasks.find(task => task.id == taskId); 

        if (!task) return;

        if (event.target.classList.contains('boardzone')) {
            const boardName = event.target.parentElement.querySelector('.board-name').innerText;

            if (boardName === 'Открытo') {
                task.status = 'Открыт';
            } else if (boardName === 'В работе') {
                task.status = 'В работе';
            } else if (boardName === 'Закрыто') {
                task.status = 'Закрыт';
            }
        }
        console.log(`Текущий статус задачи ${taskId}: ${task.status}`);

        this.saveTasksToLocalStorage();
        
    },

        addTask() {   
          
            if (this.newTask.length < 5 || this.newTask.length > 25) {
            alert("Длина задачи должна быть от 5 до 25 символов!");
            return; 
            }
            
            const newTaskObj = {   
                id: this.nextTaskId,   
                text: this.newTask,   
                status: 'Открыт',   
            };   
          

 
            this.tasks.push(newTaskObj);  
            this.saveTasksToLocalStorage(); 

            this.newTask = '';   
            this.nextTaskId++;  
        }, 
        clearInput() { 
            this.newTask = ''; 
        }, 
        openPopup(task) {  
            this.selectedTask = { ...task };  
            this.isPopupVisible = true;  
        },  
        closePopup() {  
            this.isPopupVisible = false;  
            this.selectedTask = null;  
        },  
        handleApply(updatedTask) { 
            console.log('Задача применена:', updatedTask); 
            const index = this.tasks.findIndex(task => task.id === updatedTask.id); 
            if (index !== -1) { 
                this.tasks.splice(index, 1, updatedTask); 
                this.saveTasksToLocalStorage(); 
            } 
            this.closePopup();  
        },  
        handleDelete() {   
            
            this.tasks = this.tasks.filter(task => task.id !== this.selectedTask.id);   
            this.saveTasksToLocalStorage();  
            this.closePopup();  
        },  
        getTasksFromLocalStorage() { 
            const tasks = localStorage.getItem('tasks'); 
            return tasks ? JSON.parse(tasks) : []; 
            
        }, 
        getNextTaskIdFromLocalStorage() { 
            const tasks = this.getTasksFromLocalStorage(); 
            return tasks.length ? Math.max(...tasks.map(task => task.id)) + 1 : 1; 
        }, 
        saveTasksToLocalStorage() { 
            localStorage.setItem('tasks', JSON.stringify(this.tasks)); 
        }, 
        toggleTasks() {
            this.allTasksVisible = !this.allTasksVisible; 
        },
    }, 
}
</script>

<style>
@import '../assets/css/main.css';
@import '../assets/css/toDoList.css';
@import '../assets/css/table.css';
@import '../assets/css/taskList.css'; 

</style>