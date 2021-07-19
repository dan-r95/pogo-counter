<template>
  <v-app>
    <v-main>
      <v-toolbar dark color="teal">
        <v-toolbar-title>State selection</v-toolbar-title>
        <v-autocomplete
          :loading="loading"
          :items="pokemon"
          :item-text="(item) => item.name.english"
          :item-value="(item) => item"
          :search-input.sync="search"
          cache-items
          class="mx-4"
          flat
          hide-no-data
          hide-details
          label="Type in your Pokemon"
          solo-inverted
          v-model="selectedPoke"
        >
          <template v-slot:no-data>
            <v-list-item>
              <v-list-item-title>
                Search for your favorite
                <strong>Cryptocurrency</strong>
              </v-list-item-title>
            </v-list-item>
          </template>
        </v-autocomplete>
        <v-btn icon>
          <v-icon>mdi-dots-vertical</v-icon>
        </v-btn>
      </v-toolbar>
      <div v-if="selectedPoke.name">
        <v-card class="mx-auto" max-width="400">
          <v-img
            class="white--text align-end"
            :src="imgSrc"
            aspect-ratio="1.7"
            width="200px"
          ></v-img>
          <v-card-title>{{ selectedPoke.name["english"] }}</v-card-title>
          <v-card-subtitle>{{ selectedPoke.type }}</v-card-subtitle>
        </v-card>
      </div>
      {{ selectedWeakness.strengths }}
      {{ selectedWeakness.immunes }}
      {{ selectedWeakness.weaknesses }}
    </v-main>
  </v-app>
</template>

<script>
export default {
  name: "App",

  components: {},

  data() {
    return {
      pokemon: [],
      loading: false,
      items: [],
      search: null,
      imgSrc: null,
      selectedPoke: { id: null, name: null, type: null, base: null },
      weaknesses: [],
      selectedWeakness: { weaknesses: null, strengths: null, immunes: null },
    };
  },
  watch: {
    search(val) {
      console.log(val);
      val && val !== this.selectedPoke && this.querySelections(val);
    },

    selectedPoke: function (val) {
      this.imgSrc =
        "https://github.com/fanzeyi/pokemon.json/blob/master/thumbnails/" +
        String(val.id).padStart(3, "0") +
        ".png?raw=true";
      this.findWeaknesses();
    },
  },

  mounted() {
    this.selectedPoke = { id: null, name: null };

    // Simple GET request using fetch
    fetch(
      "https://raw.githubusercontent.com/fanzeyi/pokemon.json/master/pokedex.json"
    )
      .then((response) => response.json())
      .then((data) => (this.pokemon = data));

    fetch(
      "https://raw.githubusercontent.com/filipekiss/pokemon-type-chart/master/types.json"
    )
      .then((response) => response.json())
      .then((data) => (this.weaknesses = data));
  },
  methods: {
    findWeaknesses() {
      // find in pokemon

      let currentType = this.weaknesses.find(
        (item) => item.name == this.selectedPoke.type[0]
      );
      this.selectedWeakness.immunes = currentType.immunes;
      this.selectedWeakness.immunes = currentType.weaknesses;
      this.selectedWeakness.immunes = currentType.strengths;
    },

    querySelections(v) {
      this.loading = true;
      // Simulated ajax query
      setTimeout(() => {
        if (v.length > 2) {
          this.items = this.pokemon.filter((e) => {
            return (
              (e || "").name["english"]
                .toLowerCase()
                .indexOf((v || "").toLowerCase()) > -1
            );
          });
        }
        this.loading = false;
      }, 500);
    },
  },
};
</script>

<style>
.img-circle {
  border-radius: 50%;
  height: 20%;
  width: 20%;
}
</style>