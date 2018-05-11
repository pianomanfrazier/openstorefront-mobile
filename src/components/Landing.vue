<template>
  <v-content>
    <v-container>
      <SearchBar></SearchBar>
    </v-container>
  </v-content>
</template>

<script>
import SearchBar from './SearchBar';
export default {
  name: 'Landing',
  components: {
    SearchBar
  },
  data() {
    return {
      msg: '',
    };
  },
};
</script>

<style scoped>
</style>
