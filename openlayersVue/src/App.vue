<template>
  <div id="app">
    <MapContainer/> 
  </div>
</template>

<script>
import MapContainer from "./components/MapContainer.vue"
export default {
  name:"App",
  components: {
    MapContainer,
  }
}
</script>

<style>
  html , body {
    height: 100%;
    margin: 0;
  }
  #app {
    font-family: 'Lucida Sans', 'Lucida Sans Regular', 'Lucida Grande', 'Lucida Sans Unicode', Geneva, Verdana, sans-serif;
    height: 100%;
    display: block;
  }
</style>
