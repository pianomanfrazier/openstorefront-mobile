<template>
  <v-footer height="auto" class="secondary darken-3">
    <v-layout row wrap justify-center>
      <v-btn
        v-for="link in links"
        :key="link"
        color="white"
        flat
      >
        {{ link }}
      </v-btn>
      <v-flex xs12 py-3 text-xs-center white--text>
        &copy;2018 — <strong>Vuetify</strong>
      </v-flex>
    </v-layout>
  </v-footer>
</template>

<script>
export default {
  name: 'Footer',
  data() {
    return {
      links: ['Home', 'About', 'FAQ'],
    };
  },
};
</script>

<style scoped>
</style>
