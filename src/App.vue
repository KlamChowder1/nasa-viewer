<template>
  <v-content
    :style="`background-image: url(${require('./assets/stars.jpg')})`"
    class="fill-height bg-image"
  >
    <v-app>
      <v-content
        :style="`background-image: url(${require('./assets/stars.jpg')})`"
        class="fill-height bg-image"
      >
        <ImageViewer />
      </v-content>
    </v-app>
  </v-content>
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
