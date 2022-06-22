<template>
  <h1>Pokédex</h1>
  <form @submit.prevent="pullPokeInfo">
    <input
      @keydown="imgDisplayIsFalse"
      @click="imgDisplayIsFalse"
      type="text"
      id="pokeSearchText"
      name="pokeSearchText"
      placeholder="Pokémon Name.."
    />
    <input type="submit" value="Pokédex Search" />
  </form>

  <div v-if="this.searchTrue" class="pulledData">
    <h1>{{ currentPokemonName }}</h1>
    <div class="pulledimgs">
      <div class="maleDiv">
        <div class="views">
          <p>Front:</p>
          <img v-bind:src="this.imgPulls.front_default" />
        </div>
        <div class="views">
          <p>Back:</p>
          <img v-bind:src="this.imgPulls.back_default" />
        </div>
        <div class="views">
          <p>Shiny Front:</p>
          <img v-bind:src="this.imgPulls.front_shiny" />
        </div>
        <div class="views">
          <p>Shiny Back:</p>
          <img v-bind:src="this.imgPulls.back_shiny" />
        </div>
      </div>
      <div v-if="this.femaleOption" class="femaleDiv">
        <div class="views">
          <p>Female Front:</p>
          <img v-bind:src="this.imgPulls.front_female" />
        </div>
        <div class="views">
          <p>Female Back:</p>
          <img v-bind:src="this.imgPulls.back_female" />
        </div>
        <div class="views">
          <p>Shiny Female Front:</p>
          <img v-bind:src="this.imgPulls.front_shiny_female" />
        </div>
        <div class="views">
          <p>Shiny Female Back:</p>
          <img v-bind:src="this.imgPulls.back_shiny_female" />
        </div>
      </div>
    </div>
    <h2>In Game Descriptions:</h2>
    <label for="languages">Pick Language:</label>
    <select name="languages" id="languages" v-model="currentLanguage">
      <option value="english">English</option>
      <option value="spanish">Español</option>
      <option value="french">Français</option>
      <option value="japanese">日本語</option>
    </select>
    <div class="inGameDescriptions">
      <div
        v-for="object in this.pokemonUrlDataReturn"
        v-bind:key="object"
        class="descriptions"
      >
        <!-- English -->
        <p
          v-if="
            this.languageSelect === `english` &&
            object.language.name.includes(`en`)
          "
        >
          <strong>{{ capitalizeFirstLetter(object.version.name) }}:</strong>
          <br />{{ object.flavor_text }}
        </p>
        <!-- Spanish -->
        <p
          v-if="
            this.languageSelect === `spanish` &&
            object.language.name.includes(`es`)
          "
        >
          <strong>{{ capitalizeFirstLetter(object.version.name) }}:</strong>
          <br />{{ object.flavor_text }}
        </p>
        <!-- French -->
        <p
          v-if="
            this.languageSelect === `french` &&
            object.language.name.includes(`fr`)
          "
        >
          <strong>{{ capitalizeFirstLetter(object.version.name) }}:</strong>
          <br />{{ object.flavor_text }}
        </p>
        <!-- Japanese -->
        <p
          v-if="
            this.languageSelect === `japanese` &&
            object.language.name.includes(`ja`)
          "
        >
          <strong>{{ capitalizeFirstLetter(object.version.name) }}:</strong>
          <br />{{ object.flavor_text }}
        </p>
      </div>
    </div>
  </div>
</template>
<script>
import axios from "axios";
export default {
  name: "pokeSearch",
  data() {
    return {
      imgUndefined:
        "https://www.seekpng.com/png/detail/154-1548115_mario-question-block-png-mario-block.png",
      imgPulls: {
        front_default: [],
        back_default: [],
        front_shiny: [],
        back_shiny: [],
        front_female: [],
        back_female: [],
        front_shiny_female: [],
        back_shiny_female: [],
      },
      searchTrue: false,
      currentPokemonName: [],
      currentPokemonUrl: [],
      pokemonUrlDataReturn: [],
      femaleOption: [],
      currentLanguage: "",
    };
  },
  computed: {
    languageSelect: {
      get() {
        return document.getElementById("languages").value;
      },
    },
  },
  methods: {
    capitalizeFirstLetter(string) {
      return string.charAt(0).toUpperCase() + string.slice(1);
    },
    setCurrentLanguage() {
      this.currentLanguage = document.getElementById("languages").value;
      console.log(this.currentLanguage);
    },
    imgDisplay() {
      this.searchTrue = true;
    },
    imgDisplayIsFalse() {
      this.searchTrue = false;
    },
    async pokemonUrlData() {
      let pokemonUrl = await axios.get(this.currentPokemonUrl);
      console.log(pokemonUrl);
      this.pokemonUrlDataReturn = pokemonUrl.data.flavor_text_entries;
      this.femaleOption = pokemonUrl.data.has_gender_differences;
    },
    async pullPokeInfo(submitEvent) {
      let baseUrl =
        "https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/";
      let pulledInfo = await axios.get(
        `https://pokeapi.co/api/v2/pokemon/${submitEvent.target.elements.pokeSearchText.value}`
      );
      console.log(pulledInfo);
      this.currentPokemonName = this.capitalizeFirstLetter(
        pulledInfo.data.name
      );
      this.currentPokemonUrl = pulledInfo.data.species.url;

      this.imgPulls.front_default = `${baseUrl}${pulledInfo.data.id}.png`;
      this.imgPulls.back_default = `${baseUrl}back/${pulledInfo.data.id}.png`;
      this.imgPulls.front_shiny = `${baseUrl}shiny/${pulledInfo.data.id}.png`;
      this.imgPulls.back_shiny = `${baseUrl}back/shiny/${pulledInfo.data.id}.png`;
      this.imgPulls.front_female = `${baseUrl}female/${pulledInfo.data.id}.png`;
      this.imgPulls.back_female = `${baseUrl}back/female/${pulledInfo.data.id}.png`;
      this.imgPulls.front_shiny_female = `${baseUrl}shiny/female/${pulledInfo.data.id}.png`;
      this.imgPulls.back_shiny_female = `${baseUrl}back/shiny/female/${pulledInfo.data.id}.png`;

      this.pokemonUrlData();
      this.imgDisplay();
    },
  },
  // mounted() {
  //   this.pullPokeInfo();
  // },
};
</script>
<style scoped>
h1 {
  font-size: 3rem;
  color: rgba(256, 0, 0, 0.7);
}
form input {
  background: #aaa;
  font-size: 1.5rem;
  height: 2rem;
}
form input[type="submit"] {
  background: rgba(256, 0, 0, 0.7);
  margin-left: 1rem;
}
.pulledData {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
}
.pulledimgs {
  display: flex;
  flex-direction: column;
  color: #fff;
  background: #333;
}
.maleDiv,
.femaleDiv {
  display: flex;
}
.inGameDescriptions {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
}
.views {
  border: 1px solid #000;
  height: 10rem;
  width: 10rem;
  padding: 1rem;
}
.descriptions {
  height: 17rem;
  width: 10rem;
  margin: 1rem;
  border: 1px solid #000;
}
</style>