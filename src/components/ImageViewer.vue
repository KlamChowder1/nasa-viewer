<template>
  <div>
    <h1>Welcome to Kevin Lam's Mars Photo Buying Site</h1>
    <h2>
      Click on the <v-icon color="red"> mdi-heart </v-icon> to like a picture,
      and click on the <v-icon color="green"> mdi-currency-usd </v-icon> to
      purchase pictures you love!
    </h2>
    You currently have <b> {{ this.money }} </b> internet bucks. Go wild!

    <v-layout justify-center>
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
          <v-btn text color="primary" @click="$refs.menu.save(date)">
            OK
          </v-btn>
        </v-date-picker>
      </v-menu>
    </v-layout>

    <v-row>
      <v-progress-linear
        v-if="loading"
        indeterminate
        color="blue darken-2"
        class="mt-4"
      ></v-progress-linear>

      <v-col v-if="images.length === 0 && !this.loading">
        <h2>
          No images were taken by Curiosity on this date. Try another date!
        </h2>
      </v-col>

      <v-col
        v-else
        cols="12"
        sm="12"
        md="6"
        v-for="image in images"
        :key="image.id"
      >
        <v-card
          elevation="12"
          class="ma-2"
          v-if="!boughtImagesID.includes(image.id)"
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
          <div v-if="likedImagesID.includes(image.id)" class="heart"></div>
          <v-card-actions class="justify-center">
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  icon
                  color="red"
                  @click="likeImage(image.id)"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon> mdi-heart </v-icon>
                </v-btn>
              </template>
              <span>Like</span>
            </v-tooltip>

            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  icon
                  color="green"
                  @click="boughtImage(image.id)"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon> mdi-currency-usd </v-icon>
                  {{ cost }}
                </v-btn>
              </template>
              <span>Buy</span>
            </v-tooltip>

            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  icon
                  color="blue"
                  :href="image.img_src"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon>mdi-share</v-icon>
                </v-btn>
              </template>
              <span>Share</span>
            </v-tooltip>
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
      money: 75,
      cost: 25,
      hearts: [],
      likedImagesID: [],
      boughtImagesID: [],
      loading: true,
      date: '2021-09-18',
      menu: false,
    };
  },
  mounted() {
    this.getImages();
  },
  methods: {
    async getImages() {
      await axios
        .get(
          `https://api.nasa.gov/mars-photos/api/v1/rovers/curiosity/photos?earth_date=${this.date}&api_key=${process.env.VUE_APP_NASA_API_KEY}`
        )
        .then((response) => {
          this.images = response.data.photos;
          this.loading = false;
        })
        .catch(() => {
          this.errorMessage = 'Could not fetch images.';
          this.error = true;
        });
    },
    boughtImage(imageID) {
      this.boughtImagesID.push(imageID);
      this.money -= this.cost;
    },
    likeImage(imageID) {
      const index = this.likedImagesID.indexOf(imageID);
      if (index > -1) {
        this.likedImagesID.splice(index, 1);
      } else {
        this.likedImagesID.push(imageID);
      }
      console.log(this.likedImagesID);
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