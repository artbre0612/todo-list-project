<script setup>
  import TodoApp from './components/TodoApp.vue';
</script>


<template>
  <div id="app">
    <h1>To-Do List</h1>
    <TodoApp />
  </div>
</template>

<style>
*{
  margin: 0; 
  padding: 0; 
  box-sizing: border-box;
}
html {
  background: linear-gradient(to top, #09203f 0%, #537895 100%);
}
  #app{
    min-height: 100vh;
    max-width: 70vw;
    margin: 60px auto;
  }
</style>

<style scoped>
  h1 {
    color: #e3e3e3;
    margin-bottom: 50px;
  }
</style>
