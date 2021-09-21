<template>
  <div>
    <h1>Welcome to Kevin Lam's Mars Photo Buying Site!</h1>
    <h2>
      Click on the <v-icon color="red"> mdi-heart </v-icon> to like a picture,
      and click on the <v-icon color="green"> mdi-currency-usd </v-icon> to
      purchase pictures you love!
    </h2>
    You currently have <b> {{ this.money }} </b> internet bucks!
    <v-progress-linear
      v-if="loading"
      indeterminate
      color="blue darken-2"
    ></v-progress-linear>

    <v-menu
      ref="menu"
      v-model="menu"
      :close-on-content-click="false"
      :return-value.sync="date"
      transition="scale-transition"
      offset-y
      min-width="auto"
    >
      <template v-slot:activator="{ on, attrs }">
        <v-text-field
          v-model="date"
          label="Pick a date"
          prepend-icon="mdi-calendar"
          readonly
          v-bind="attrs"
          v-on="on"
          hint="Click 'Ok' to save date selection"
          persistent-hint
        ></v-text-field>
      </template>
      <v-date-picker v-model="date" no-title scrollable>
        <v-spacer></v-spacer>
        <v-btn text color="primary" @click="menu = false"> Cancel </v-btn>
        <v-btn text color="primary" @click="$refs.menu.save(date)"> OK </v-btn>
      </v-date-picker>
    </v-menu>
    <v-row>
      <v-col v-if="images.length === 0 && !this.loading">
        <h2>
          No images were taken by Curiosity on this date. Try another date!
        </h2>
      </v-col>
      <v-col v-else cols="6" v-for="image in images" :key="image.id">
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
              <v-icon> mdi-currency-usd </v-icon>
              {{ cost }}
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
      loading: true,
      date: '2021-09-18',
      menu: false,
    };
  },
  mounted() {
    // console.log(process.env.VUE_APP_NASA_API_KEY);
    this.getImages();
  },
  methods: {
    getImages() {
      axios
        .get(
          `https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=${this.date}&api_key=${process.env.VUE_APP_NASA_API_KEY}`
        )
        .then((response) => {
          console.log(response.data.photos);
          this.images = response.data.photos;
          this.loading = false;
        });
    },
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
  watch: {
    date() {
      this.loading = true;
      this.getImages();
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