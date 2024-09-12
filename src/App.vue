<template>
  <HeaderComponent/>
  <ToDoList/>
  <FooterComponent/>
  
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue';
import ToDoList from './components/ToDoList.vue';
import FooterComponent from './components/FooterComponent.vue';


export default {
  name: 'App',
  components: {
    HeaderComponent,
    ToDoList,
    FooterComponent,
  }
}
</script>

<style>
*{
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

</style>
