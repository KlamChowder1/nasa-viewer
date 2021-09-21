<template>
  <v-main
    :style="`background-image: url(${require('./assets/stars.jpg')})`"
    class="fill-height bg-image"
  >
    <v-app style="background: rgba(0, 0, 0, 0)">
      <ImageStore />
    </v-app>
  </v-main>
</template>

<script>
import ImageStore from './components/ImageStore';

export default {
  name: 'App',
  components: {
    ImageStore,
  },
};
</script>

<style scoped>
.bg-image {
  background-repeat: no-repeat;
  background-position: center center;
  background-attachment: fixed;
  background-size: cover;
}

#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
