<template>
<div>
  <v-toolbar color="primary" dark>
    <v-toolbar-title>Title</v-toolbar-title>
    <v-spacer></v-spacer>
    <v-toolbar-side-icon></v-toolbar-side-icon>
  </v-toolbar>
  <hr>
</div>
</template>

<script>
export default {
  name: 'Header',
  data() {
    return {
      msg: '',
    };
  },
};
</script>

<style scoped>
hr {
  width: 100%;
  height: 5px;
  background-color: #F8C436;
}
</style>
