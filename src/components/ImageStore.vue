<template>
  <div>
    <v-row justify="center">
      <v-card width="65vw" justify-center>
        <h1>
          Welcome to <a href="https://www.linktr.ee/kevinkflam">Kevin Lam</a>'s
          Mars Picture Shop
        </h1>
        <h2>
          Click on the <v-icon color="red"> mdi-heart </v-icon> to like a
          picture, <v-icon color="green"> mdi-currency-usd </v-icon> to
          purchase, and <v-icon color="blue"> mdi-share </v-icon> to share!
        </h2>
        You currently have <b> {{ this.userWallet }} </b> internet bucks.

        {{ this.userWallet ? 'Go wild!' : 'Contact Kevin for more funds!' }}

        <!-- Date picker -->
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
                class="shrink"
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
      </v-card>
    </v-row>

    <v-row>
      <!-- Progress bar while fetching from API-->
      <v-progress-linear
        v-if="loading"
        indeterminate
        color="#c1440e"
        class="mt-4"
      ></v-progress-linear>

      <v-col v-if="images.length === 0 && !this.loading">
        <h2 style="color: white">
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
          <!-- Image info -->
          <v-card-title>
            {{ image.id }}_{{ image.camera.full_name }}
          </v-card-title>
          📷 Credits:
          <a href="https://www.instagram.com/marscuriosity/">{{
            image.rover.name
          }}</a>
          on {{ image.earth_date }} 📅
          <img
            :src="image.img_src"
            contain
            height="100%"
            width="100%"
            :alt="image.id + '_' + image.camera.full_name"
          />
          <div v-if="likedImagesID.includes(image.id)" class="heart"></div>

          <v-card-actions class="justify-center">
            <!-- Liking an image -->
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  :aria-label="'like-image-' + image.id"
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

            <!-- Buying an image -->
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  :aria-label="'buy-image-' + image.id"
                  icon
                  color="green darken-4"
                  @click="buyImage(image.id)"
                  v-bind="attrs"
                  v-on="on"
                >
                  <v-icon> mdi-currency-usd </v-icon>
                  {{ imageCost }}
                </v-btn>
              </template>
              <span>Buy</span>
            </v-tooltip>

            <!-- Sharing an image -->
            <v-tooltip bottom>
              <template v-slot:activator="{ on, attrs }">
                <v-btn
                  :aria-label="'share-image-' + image.id"
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

    <!-- Snackbar notifications -->
    <v-snackbar v-model="snackbar" :timeout="timeout">
      {{ this.snackbarMessage }}
      <template v-slot:action="{ attrs }">
        <v-btn color="red" text v-bind="attrs" @click="snackbar = false">
          <v-icon> mdi-close </v-icon>
        </v-btn>
      </template>
    </v-snackbar>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  name: 'ImageStore',
  data() {
    return {
      //  datepicker
      menu: false,

      // image store states
      images: [],
      loading: true,
      date: '2021-09-18',

      // liked images
      likedImagesID: [],

      // buying an image
      userWallet: 100,
      imageCost: 25,
      boughtImagesID: [],

      // snackbar notifications
      snackbar: false,
      snackbarMessage: 'Something went wrong.',
      timeout: 5000,
    };
  },
  watch: {
    date() {
      this.loading = true;
      this.getImages();
    },
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
          this.snackbarMessage = 'Could not fetch images.';
          this.snackbar = true;
        });
    },
    likeImage(imageID) {
      const index = this.likedImagesID.indexOf(imageID);
      if (index > -1) {
        this.likedImagesID.splice(index, 1);
      } else {
        this.likedImagesID.push(imageID);
      }
    },
    buyImage(imageID) {
      if (this.userWallet < this.imageCost) {
        this.snackbarMessage =
          "Uh oh, you don't have enough internet bucks... hire Kevin to get unlimited internet bucks!";
        this.snackbar = true;
      } else {
        this.boughtImagesID.push(imageID);
        this.userWallet -= this.imageCost;
        this.snackbarMessage = 'Picture purchased successfully!';
        this.snackbar = true;
      }
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