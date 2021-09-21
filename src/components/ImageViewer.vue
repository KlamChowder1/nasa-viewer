<template>
  <div>
    <h1>Welcome to Kevin Lam's Mars Photo Buying Site!</h1>
    <h3>
      All photos are taken by NASA. I am the middleman providing you a quick and
      easy way to download the photos for a few measly internet bucks. <br />
      (Please don't right click the images and 'Save image as', as this would
      ruin my business model and
      <a
        href="https://i.kym-cdn.com/entries/icons/original/000/033/376/tiger.jpg"
        >I will never financially recover from this </a
      >)
    </h3>
    You currently have <b> {{ this.money }} </b> internet bucks!
    <v-row>
      <v-col cols="6" v-for="image in images" :key="image.id">
        <v-card elevation="12" class="ma-2">
          <v-card-title>
            {{ image.id }}_{{ image.camera.full_name }}
          </v-card-title>
          📷 Credits:
          <a href="https://www.instagram.com/marscuriosity/">{{
            image.rover.name
          }}</a>
          on {{ image.earth_date }}
          <img :src="image.img_src" contain height="100%" width="100%" />
          <v-card-actions class="justify-center">
            <v-btn icon color="red">
              <v-icon> mdi-heart </v-icon>
            </v-btn>
            <v-btn icon color="green" @click="downloadImage()">
              <v-icon> mdi-download </v-icon>
              ${{ cost }}
            </v-btn>
          </v-card-actions>
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
      money: 250,
      cost: 25,
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
  methods: {
    downloadImage() {
      this.money -= this.cost;
    },
  },
};
</script>