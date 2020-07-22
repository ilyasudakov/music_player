<template>
  <div id="app">
    <MusicPlayer />
  </div>
</template>

<script>
import MusicPlayer from './components/MusicPlayer.vue'

export default {
  name: 'App',
  components: {
    MusicPlayer,
  },
}
</script>

<style>
* {
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  width: 100%;
  height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
</style>
