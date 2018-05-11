<template>
  <div id="app">
    <v-app>
    <!-- header nav component -->
    <Header></Header>
    <router-view/>
    <Footer></Footer>
    <!-- footer -->
    </v-app>
  </div>
</template>

<script>
import Header from './components/Header';
import Footer from './components/Footer';
export default {
  name: 'App',
  components: {
    Footer,
    Header
  }
};
</script>

<style>
</style>
