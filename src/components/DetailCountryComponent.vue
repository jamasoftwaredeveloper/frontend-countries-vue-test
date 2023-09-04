<template>
  <div>
    <!-- <b-container class="bv-example-row"> -->
    <b-button variant="primary" @click="back()" class="mb-2">Back</b-button>
    <b-card no-body class="overflow-hidden">
      <b-row>
        <b-col md="12" sm="12" lg="6" xl="6">
          <b-card-img
            :src="country.flags.svg"
            alt="Image"
            class="rounded-0"
          ></b-card-img>
        </b-col>
        <b-col md="12" sm="12" lg="6" xl="6">
          <b-card-body
            :title="country.name.common"
            class="justify-content-center"
          >
            <b-card-text>
              <br />
              <b>Name official:</b> {{ country.name.official }}<br />
              <b>Capital:</b> {{ country.capital[0] }}<br />
              <b>Region:</b> {{ country.region }}<br />
              <b>Sub region:</b> {{ country.subregion }}<br />
              <b>Borders:</b><br />
              <ul class="navega">
                <li
                  v-for="(border, index) in country.borders"
                  v-bind:key="index"
                >
                  <b-link @click="detailBorder(border, $bvModal)">{{
                    border
                  }}</b-link
                  >,
                </li>
              </ul>
              <b>Maps:</b><br />
              <b-link :href="country.maps.googleMaps" target="_blank"
                >Google maps</b-link
              >
              <br />
              <b-link :href="country.maps.openStreetMaps" target="_blank"
                >Open street maps</b-link
              >
              <br />
              <b-modal id="bv-modal-border" hide-footer>
                <template #modal-title>
                  <b-link @click="detailCountry(border.name, $bvModal)">{{
                    border.name
                  }}</b-link>
                </template>
                <div class="d-block text-justify">
                  <b-card-img
                    :src="border.svg"
                    alt="Image"
                    class="rounded-0"
                    style="height: 20rem; max-width: 30rem"
                  ></b-card-img>
                  <br />
                  <b>Name official:</b> {{ border.official }}<br />
                  <b>Capital:</b> {{ border.capital }}<br />
                  <b>Region:</b> {{ border.region }}<br />
                  <b>Sub region:</b> {{ border.subregion }}<br />
                </div>
                <b-button
                  class="mt-3"
                  block
                  @click="$bvModal.hide('bv-modal-border')"
                  >Close</b-button
                >
              </b-modal>
            </b-card-text>
          </b-card-body>
        </b-col>
      </b-row>
    </b-card>
    <!-- </b-container> -->
  </div>
</template>
<script>
import axios from "axios";

export default {
  name: "DetailCountryComponent",
  data() {
    return {
      // Note 'isActive' is left out and will not appear in the rendered table
      country: null,
      border: {
        name: "",
        official: "",
        capital: "",
        region: "",
        subregion: "",
        svg: "",
      },
    };
  },
  mounted() {
    this.getCountryData();
  },
  methods: {
    getCountryData() {
      axios
        .get("https://restcountries.com/v3.1/name/" + this.$route.params.name)
        .then((response) => {
          this.country = response.data[0];
        })
        .catch((error) => {
          console.log(error);
        });
    },
    back() {
      this.$router.replace({ name: "home" });
    },
    detailBorder(code, $bvModal) {
      axios
        .get("https://restcountries.com/v3.1/alpha?codes=" + code)
        .then((response) => {
          this.border.name = response.data[0].name.common;
          this.border.official = response.data[0].name.official;
          this.border.capital = response.data[0].capital[0];
          this.border.region = response.data[0].region;
          this.border.subregion = response.data[0].subregion;
          this.border.svg = response.data[0].flags.svg;
        })
        .catch((error) => {
          console.log(error);
        })
        .finally(() => {
          $bvModal.show("bv-modal-border");
        });
    },
    detailCountry(name, $bvModal) {
      this.$route.params.name = name;
      this.$router.replace({ name: "detail" });
      this.getCountryData();
      $bvModal.hide("bv-modal-border");
    },
  },
};
</script>
<style scoped>
ul.navega li {
  display: inline;
}
</style>
