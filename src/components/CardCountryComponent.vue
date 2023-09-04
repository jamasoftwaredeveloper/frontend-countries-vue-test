<template>
  <div>
    <b-row class="mt-3" v-if="items.length > 0">
      <b-card>
        <b-row>
          <b-col cols="12" sm="6" md="8" lg="8" xl="8">
            <b-form-input
              id="input-1"
              v-model="search"
              type="email"
              placeholder="Search country"
              required
              class="mb-2"
            ></b-form-input>
          </b-col>
          <b-col cols="12" sm="6" md="4" lg="4" xl="4">
            <div>
              <b-form-select
                v-model="selected"
                :options="regions"
              ></b-form-select>
            </div>
          </b-col>
          <!-- <b-col cols="12" class="d-flex justify-content-center mb-3">
            <div
              class="d-flex justify-content-center mb-3"
              v-if="items.length == 0"
            >
              <b-spinner
                variant="primary"
                style="width: 3rem; height: 3rem"
              ></b-spinner>
            </div>
          </b-col> -->
          <b-col
            cols="12"
            sm="6"
            md="4"
            lg="3"
            xl="3"
            v-for="(item, index) in items"
            v-bind:key="index"
          >
            <b-card
              :id="item.idd.suffixes"
              tag="article"
              @click="detailCountry(item.name.common)"
            >
              <b-card-img
                :src="item.flags ? item.flags.svg : ''"
                alt="Image"
                class="rounded-0"
                style="height: 9rem; font-size: 10px"
              ></b-card-img>
              <br />
              <b-card-text>
                <br />
                <b>{{ item.name.common }}</b> <br />
                <b>Population:</b> {{ item.population }} <br /><b>Region:</b>
                {{ item.region }} <br />
                <b>Capital:</b>
              </b-card-text>
            </b-card>

            <br />
          </b-col>
        </b-row>
      </b-card>
    </b-row>
    <div class="text-center" v-else>
      <b-spinner variant="primary"></b-spinner>
    </div>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "CardCountryComponent",
  data() {
    return {
      search: "",
      countries: [],
      selected: null,
      regions: [
        { value: null, text: "Please select an option" },
        { value: "Americas", text: "Americas" },
        { value: "Asia", text: "Asia" },
        { value: "Europe", text: "Europe" },
        { value: "Africa", text: "Africa" },
        { value: "Oceania", text: "Oceania" },
        { value: "Antarctic", text: "Antarctic" },
      ],
    };
  },
  mounted() {
    this.getCountriesData();
  },
  methods: {
    getCountriesData() {
      axios
        .get("https://restcountries.com/v3.1/all")
        .then((response) => {
          this.countries = response.data;
        })
        .catch((error) => {
          console.log(error);
        });
    },

    detailCountry(name) {
      this.$route.params.name = name;
      this.$router.replace({ name: "detail" });
    },
  },
  computed: {
    items() {
      if (this.selected != null && this.search == "") {
        console.log(this.selected);
        console.log(this.search == "");
        return this.countries.filter((item) => {
          return item.region
            .toLowerCase()
            .includes(this.selected.toLowerCase());
        });
      } else {
        console.log(this.search == "");
        return this.countries.filter((item) => {
          return (
            item.name.common
              .toLowerCase()
              .includes(this.search.toLowerCase()) &&
            item.region
              .toLowerCase()
              .includes(
                this.selected != null ? this.selected.toLowerCase() : ""
              )
          );
        });
      }
    },
  },
};
</script>

<style>
.action-item:hover {
  cursor: pointer;
}
ul.navega li {
  display: inline;
}
</style>
