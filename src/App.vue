<template>
  <HelloWorld />
  <MindMap></MindMap>
</template>

<script>
import HelloWorld from './components/HelloWorld.vue'
import MindMap from './components/MindMap.vue';

export default {
  name: 'App',
  components: {
    HelloWorld,
    MindMap
}
}
</script>

<style>
body{
  margin:0px;
  padding: 0px;
}

#app {
  margin:0px;
  padding: 0px;
}
</style>
