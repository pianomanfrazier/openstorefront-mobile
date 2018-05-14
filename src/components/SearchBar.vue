<template>
<div>
  <form v-on:submit.prevent="newSearch()" >
    <div class="searchbar">
      <input v-model="searchQuery" class="searchfield" type="text" placeholder="Search">
      <v-icon class="icon">search</v-icon>
    </div>

    <v-btn small @click="show = !show" flat>
      <v-icon v-if="show">arrow_drop_up</v-icon><v-icon v-if="!show">arrow_drop_down</v-icon>
      Add Search Filters
    </v-btn>
    <v-btn small @click="clear()" v-if="selected.length !== 0">Clear Filters</v-btn>
    <div v-if="selected.length !== 0" style="padding: 0 0.5em 0.8em 0.8em;">
      <transition-group name="fade">
        <div
          class="pill"
          style="display: inline-block"
          v-for="item in selected"
          v-bind:key="item">
          {{ item }}
          <div
            style="display: inline-block; cursor: pointer;"
            @click="deleteItem(item)"
          >&times;</div>
        </div>
      </transition-group>
    </div>

    <div v-if="show" transition="slide-y-transition">
      <v-list>
        <v-expansion-panel>
          <v-expansion-panel-content
            v-for="item in items"
            :key="item.label"
            expand-icon="arrow_drop_down"
          >
            <div slot="header">{{ item.label }}</div>
            <v-list-tile v-for="data in item.data" :key="data">
              <v-list-tile-action>
                <v-checkbox v-model="selected" :value="data"></v-checkbox>
              </v-list-tile-action>
              <v-list-tile-title>{{ data }}</v-list-tile-title>
            </v-list-tile>
          </v-expansion-panel-content>
        </v-expansion-panel>
      </v-list>
    </div>
  </form>
  <!-- show some results on text entry -->

  <h2 v-if="searchResults.data" style="text-align: center">Search Results</h2>
  <p v-if="searchResults.data">
    <span v-if="searchQueryIsDirty">Fetching</span><span v-else>Showing</span>
    results {{ offset + 1 }} - {{ totalSearchResults > offset + searchPageSize ? offset + searchPageSize : totalSearchResults }} of
    {{ searchResults.data.totalNumber }} results</p>
  <v-progress-circular
    v-if="searchQueryIsDirty"
    indeterminate color="primary"
  ></v-progress-circular>

  <div v-bind:class="{old: searchQueryIsDirty}">
    <div v-if="searchResults.data">

      <ul>
        <li v-for="item in searchResults.data.data" :key="item">
          {{ item.name }}
        </li>
      </ul>

      <div style="text-align: center;">
        <v-btn
          flat
          icon
          v-if="offset > 0" @click="prevPage()">
        <v-icon x-large>chevron_left</v-icon>
        </v-btn>
        <v-btn
          flat
          icon
          v-if="offset + searchPageSize < totalSearchResults" @click="nextPage()">
          <v-icon x-large>chevron_right</v-icon>
        </v-btn>
      </div>

    </div>
  </div>

  <div v-if="errors.length > 0">
    <ul>
      <li v-for="error in errors" :key="error">
        {{ error }}
      </li>
    </ul>
  </div>
</div>
</template>

<script>
import _ from 'lodash';
import axios from 'axios';

export default {
  name: 'SearchBar',
  methods: {
    clear() {
      this.selected = [];
    },
    deleteItem(item) {
      this.selected = _.remove(this.selected, n => n !== item);
    },
    submitSearch() {
      this.searchQueryIsDirty = true;
      let that = this;
      axios
        .post(`/openstorefront/api/v1/service/search/advance?paging=true&sortField=searchScore&sortOrder=${that.searchSortOrder}&offset=${that.searchPage * that.searchPageSize}&max=${that.searchPageSize}`, {
          searchElements: [
            {
              mergeCondition: 'AND',
              searchType: 'INDEX',
              value: that.searchQuery.trim() ? `*${that.searchQuery}*` : '***',
            },
          ],
        })
        .then((response) => {
          that.searchResults = response;
          that.totalSearchResults = response.data.totalNumber;
          that.searchQueryIsDirty = false;
        })
        .catch(e => (this.errors.push(e)))
        .finally(() => { that.searchQueryIsDirty = false; });
    },
    newSearch() {
      this.searchPage = 0;
      this.submitSearch();
    },
    nextPage() {
      this.searchPage += 1;
      this.submitSearch();
    },
    prevPage() {
      if (this.searchPage > 0) {
        this.searchPage -= 1;
        this.submitSearch();
      }
    },
  },
  watch: {
    // searchQuery: _.throttle(function () {
    //   this.searchQueryIsDirty = true;
    //   // some expensive query
    // }, 1000),
  },
  computed: {
    offset() {
      return this.searchPage * this.searchPageSize;
    },
  },
  data() {
    return {
      items: [
        { label: 'Categories', color: 'teal', data: ['Shooters', 'Telescopes', 'Rockets', 'Propulsion', 'Blasters', 'Guns', 'Bullets', 'Gear'] },
        { label: 'Attributes', color: 'cyan', data: ['Mass', 'Color', 'Volume'] },
        { label: 'Organizations', color: 'blue', data: ['NASA', 'Navy', 'SpaceX', 'Starsem', 'Orbital ATK', 'Lockheed Martin', 'Rockwell'] },
      ],
      selected: [],
      show: false,
      searchQuery: '',
      searchResults: {},
      searchQueryIsDirty: false,
      errors: [],
      searchPage: 0,
      searchPageSize: 10,
      totalSearchResults: 0,
      searchSortOrder: 'DESC',
    };
  },
};
</script>

<style scoped>
.old {
  color: #999;
}
.searchbar {
  border-radius: 2px;
  box-shadow: 0 3px 1px -2px rgba(0,0,0,.2),0 2px 2px 0 rgba(0,0,0,.14),0 1px 5px 0 rgba(0,0,0,.12);
  padding: 0.7em 1.2em;
  margin-bottom: 0.3em;
  font-size: 120%;
  transition: box-shadow 0.7s;
}
/* .searchbar:hover {
  box-shadow: 0 3px 4px -2px rgba(0,0,0,.4),0 2px 4px 0 rgba(0,0,0,.18),0 1px 5px 0 rgba(0,0,0,.16);
} */
.searchfield {
  display: inline-block;
  width: 80%;
}
.icon {
  float: right;
}
input {
    caret-color: #3467C0;
}
input:focus {
  outline: none;
}
input:focus + .icon {
  color: #3467C0;
}
.pill {
  border-radius: 10px;
  color: white;
  background-color:#555;
  padding: 0.1em 0.5em;
  margin-right: 0.3em;
  margin-top: 0.3em;
}
.fade-enter-active, {
  transition: opacity .2s;
}
.fade-leave-active {
  transition: opacity .1s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}
</style>
