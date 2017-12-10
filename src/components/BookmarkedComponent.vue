<template>
  <div class="BookmarkedComponent">
      <br clear="both">
      Bookmarked:
      <br>
      <br clear="both">
      <div class="media-list">
        <div class="media" v-for="result in results">
          <div class="media-left">
            <a v-bind:href="result.resourceURI" target="_blank">
              <img class="media-object" v-bind:src="result.thumbnail.path+'.'+result.thumbnail.extension">
            </a>
          </div>
          <div class="media-body">
            <p class="media-heading"><a v-bind:href="result.resourceURI" target="_blank">{{result.name}}</a></p>
            <a href="#"><img :src='bookmarkedIcon' v-on:click="unBookmark($event, result.id)" alt="bookmark" title="Unbookmark" class="bookmarkIcon"></a>
          </div>
        </div>
      </div>
      <br>
  </div>
</template>

<script>
import axios from 'axios'
export default {
  name: 'BookmarkedComponent',
  props: ['bookmarkedA'],
  data () {
    return {
      results: [],
      bookmarked: '',
      bookmarkedIcon: '/src/assets/bookmarked.png',
      bookmarkIcon: '/src/assets/bookmark.png'
    }
  },
  mounted: function () {
    this.bookmarked = localStorage.getItem('bookmarked')
    if (this.bookmarked) {
      this.bookmarked = this.bookmarked.split(',')
    }

    if (this.bookmarked === null || this.bookmarked === '') {
      this.bookmarked = []
    }
    this.updateBookmarked()
  },
  methods: {
    unBookmark: function (event, characterID) {
      let aBookmarked = this.bookmarked.filter((bookmark) => {
        return bookmark.toString() !== characterID.toString()
      })
      event.target.src = this.bookmarkIcon
      this.bookmarked = aBookmarked
      localStorage.setItem('bookmarked', this.bookmarked.join())
      this.updateBookmarked()
    },

    updateBookmarked: function () {
      const vm = this
      vm.results = []
      this.bookmarked.forEach((bookmark) => {
        let url = 'https://gateway.marvel.com:443/v1/public/characters/' + bookmark + '?apikey=11100856935dba0b2e02f59c2253125f'
        axios.get(url)
          .then(function (response) {
            let responseData = response.data.data.results
            vm.results.push(responseData[0])
          })
          .catch(function (error) {
            console.log(error)
          })
      })
    }
  },
  created: function () {
    let bTest = localStorage.getItem('bookmarked')

    if (!bTest) {
      bTest = []
      localStorage.setItem('bookmarked', bTest)
    }
  },
  watch: {
    bookmarkedA: function (val) {
      this.bookmarked = localStorage.getItem('bookmarked').split(',')
      this.updateBookmarked()
    }
  }
}
</script>
