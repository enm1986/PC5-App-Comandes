<template>
  <div id="app">
    <!--
    <img alt="Vue logo" src="./assets/logo.png">
    <HelloWorld msg="Welcome to Your Vue.js App"/>
    -->
    <PrepareOrder />
    <ListOrders />
  </div>
</template>

<script>
//import HelloWorld from './components/HelloWorld.vue'
import PrepareOrder from "./components/PrepareOrder.vue";
import ListOrders from "./components/ListOrders.vue";

export default {
  name: "App",
  components: {
    //HelloWorld
    PrepareOrder,
    ListOrders
  }
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  color: #2c3e50;
  margin-top: 60px;
  display: flex;
  justify-content: space-between;
}
</style>
