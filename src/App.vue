<template>
  <div id="app">
    <img src="./assets/logo.png">
    <router-view/>
  </div>
</template>

<script>
export default {
  name: 'App'
}

//动态设置字体大小
document.addEventListener('DOMContentLoaded',()=>{
  const html=document.querySelector('html');

  let fontSize=window.innerWidth/10>40?40:window.innerWidth/10;
  html.style.fontSize=fontSize+'px';
});
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
