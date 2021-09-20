<template>
  <div>
    hello
    {{ this.images }}
    <img v-for="image in images" :src="image.img_src" :key="image.id" />
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
        `https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=2021-9-19&api_key=${process.env.VUE_APP_NASA_API_KEY}`
      )
      .then((response) => (this.images = response.data.photos));
  },
};
</script>