<template>
  <div class="MainComponent">
      <h1 class="center" >{{ greeting }}</h1>
      <Search v-on:searchTermChanged="searchTermChanged"></Search>
      <br>
      <Results v-bind:term="term" v-on:bookmarkChanged="bookmarkChanged"></Results>
      <br clear="both">
  </div>
</template>

<script>

import Search from '@/components/Search'
import Results from '@/components/Results'

export default {
  name: 'MainComponent',
  data () {
    return {
      greeting: 'Welcome to Marvel Charater Searcher!',
      term: '',
      bookmarked: []
    }
  },
  components: {
    Search,
    Results
  },
  methods: {
    searchTermChanged: function (term) {
      this.term = term
    },

    bookmarkChanged: function (bookmarks) {
      this.bookmarked = bookmarks
    }
  }
}
</script>
