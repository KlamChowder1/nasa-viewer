<template>
  <ImageViewer />
</template>

<script>
import ImageViewer from './components/ImageViewer';

export default {
  name: 'App',
  components: {
    ImageViewer,
  },
};
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
</style>
