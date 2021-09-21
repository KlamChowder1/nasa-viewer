<template>
  <div>
    <!-- hello
    {{ this.images }} -->
    <v-row>
      <v-col cols="6" v-for="image in images" :key="image.id">
        <v-card>
          <v-card-title>
            {{ image.id }}_{{ image.camera.full_name }}
          </v-card-title>
          📷 Credits:
          <a href="https://www.instagram.com/marscuriosity/">{{
            image.rover.name
          }}</a>
          on {{ image.earth_date }}
          <img :src="image.img_src" contain height="50%" width="100%" />
        </v-card>
      </v-col>
    </v-row>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'ImageViewer',
  data() {
    return {
      images: [],
    };
  },
  mounted() {
    // console.log(process.env.VUE_APP_NASA_API_KEY);
    axios
      .get(
        `https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=2021-9-18&api_key=${process.env.VUE_APP_NASA_API_KEY}`
      )
      .then((response) => {
        console.log(response.data.photos);
        this.images = response.data.photos;
      });
  },
};
</script>