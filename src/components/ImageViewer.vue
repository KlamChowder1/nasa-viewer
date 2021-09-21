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
        <v-card
          elevation="12"
          class="ma-2"
          v-if="!boughtImages.includes(image.id)"
        >
          <v-card-title>
            {{ image.id }}_{{ image.camera.full_name }}
          </v-card-title>
          📷 Credits:
          <a href="https://www.instagram.com/marscuriosity/">{{
            image.rover.name
          }}</a>
          on {{ image.earth_date }}
          <img :src="image.img_src" contain height="100%" width="100%" />
          <div v-if="likedImages.includes(image.id)" class="heart"></div>
          <v-card-actions class="justify-center">
            <v-btn icon color="red" @click="likeImage(image.id)">
              <v-icon> mdi-heart </v-icon>
            </v-btn>
            <v-btn icon color="green" @click="boughtImage(image.id)">
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
      hearts: [],
      likedImages: [],
      boughtImages: [],
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
    boughtImage(imageID) {
      this.boughtImages.push(imageID);
      this.money -= this.cost;
    },
    likeImage(imageID) {
      const index = this.likedImages.indexOf(imageID);
      if (index > -1) {
        this.likedImages.splice(index, 1);
      } else {
        this.likedImages.push(imageID);
      }
      console.log(this.likedImages);
    },
  },
};
</script>

<style scoped>
@keyframes heartfade {
  0% {
    opacity: 1;
  }
  25% {
    opacity: 0;
  }
  50% {
    opacity: 1;
  }
  75% {
    opacity: 0;
  }
}
.heart {
  z-index: 999;
  animation: heartfade 6s linear;
  position: absolute;
  animation-iteration-count: infinite;
}
.heart:before,
.heart:after {
  content: '';
  background-color: #fc2a62;
  position: absolute;
  height: 30px;
  width: 45px;
  border-radius: 15px 0px 0px 15px;
}

.heart:before {
  transform: rotate(45deg);
}

.heart:after {
  left: 10.5px;
  transform: rotate(135deg);
}
</style>