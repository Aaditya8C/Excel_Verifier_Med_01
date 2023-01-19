<template>
  <div class="container">
    <HelloWorld msg="Verify your Excel Files"/>
    <excelImport/>
  </div>
  
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import excelImport from './components/excelImport.vue';
export default {
  name: 'App',
  components: {
    HelloWorld,
    excelImport
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.container{
  /* background-color:  #7EC8E3; */
}
</style>
