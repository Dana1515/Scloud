<template>
  <HeaderComponent/>
  <ToDoListComponent  />
  <FooterComponent/>
  
</template>

<script>
import HeaderComponent from './components/HeaderComponent.vue';
import ToDoListComponent from './ToDoListComponent.vue';
import FooterComponent from './components/FooterComponent.vue';


export default {
  name: 'App',
  components: {
    HeaderComponent,
    ToDoListComponent,
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
