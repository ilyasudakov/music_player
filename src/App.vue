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
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
</style>
