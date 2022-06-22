<template>
  <pokeSearch />
</template>

<script>
import pokeSearch from "./components/PokeSearch.vue";
export default {
  name: "App",
  components: {
    pokeSearch,
  },
};
</script>
<style>
* {
  background: #333;
  color: #fff;
}
</style>
