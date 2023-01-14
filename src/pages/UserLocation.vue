<template>
  <section class="ui two column centered grid">
    <div class="column">
      <form class="ui segment large form">
        <div class="ui message red" v-show="error">{{ error }}</div>
        <div class="ui segment">
          <div class="field">
            <div
              class="ui right icon input large"
              :class="{ loading: spinner }"
            >
              <input
                type="text"
                placeholder="Enter your address"
                v-model="address"
              />
              <i class="dot circle link icon" @click="locatorButtonPressed"></i>
            </div>
          </div>
          <button class="ui button">Go</button>
        </div>
      </form>
    </div>
  </section>
</template>

<script>
import axios from "axios";

export default {
  data() {
    return {
      address: "",
      error: "",
      spinner: false
    };
  },

  methods: {
    locatorButtonPressed() {
      this.spinner = true;

      if (navigator.geolocation) {
        navigator.geolocation.getCurrentPosition(
          position => {
            this.getAddressFrom(
              position.coords.latitude,
              position.coords.longitude
            );
          },
          error => {
            this.error =
              "Locator is unable to find your address. Please type your address manually";
            this.spinner = false;
          }
        );
      } else {
        this.error = error.message;
        console.log("Your browser does not support geolocation API");
      }
    },
    getAddressFrom(lat, long) {
      axios
        .get(
          "https://maps.googleapis.com/maps/api/geocode/json?latlng=" +
            lat +
            "," +
            long +
            "&key=AIzaSyA6TcIPRePIJU2BYVlTOCSYmobIBL9_6VI"
        )
        .then(response => {
          if (response.data.error_message) {
            this.error = response.data.error_message;
            console.log(response.data.error_message);
          } else {
            this.address = response.data.results[0].formattes_address;
            // console.log(response.data.results[0].formattes_address);
          }
          this.spinner = false;
        })
        .catch(error => {
          this.error = error.message;
          this.spinner = false;
          console.log(error.message);
        });
    }
  }
};
</script>

<style>
.ui.button,
.dot.circle.icon {
  background-color: #ff5a5f;
  color: white;
}
</style>
