<template>
  <div id="app">
    <video-presenter></video-presenter>
  </div>
</template>

<script>
import VideoPresenter from "@/components/VideoPresenter";
export default {
  name: 'app',
  components: {
    VideoPresenter
  }
}
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
