<template>
  <div id="app">
    <HelloWorld/>
  </div>
</template>

<script>
import HelloWorld from './components/HelloWorld'

export default {
  name: 'App',
  components: {
    HelloWorld
  }
}
</script>

<style>
*{
  margin: 0;
  padding: 0;
}
html,body{
  width: 100%;
  height: 100%;
  overflow: hidden;
}
#app {
  font-family: 'Microsoft Yahei';
  width: 100%;
  height: 100%;
  background-color: #F3F3F3;
}
</style>
