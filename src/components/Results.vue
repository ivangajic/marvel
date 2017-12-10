<template>
  <div class="Results">
    <div v-if="noResults" class="no-results">
      <BookmarkedComponent v-bind:bookmarkedA="bookmarks"></BookmarkedComponent>
    </div>
      <div class="media-list">
        <div class="media" v-for="result in results">
          <div class="media-left">
            <a :href="result.urls[0].url" target="_blank">
              <img class="media-object" :src="result.thumbnail.path+'.'+result.thumbnail.extension">
            </a>
          </div>
          <div class="media-body">
            <p class="media-heading"><a :href="result.urls[0].url" target="_blank">{{result.name}}</a></p>
            <a href="#"><img :src='getIcon(result.id)' v-on:click="bookmark($event, result.id)" alt="bookmark" :title="getTitle(result.id)" class="bookmarkIcon"></a>
          </div>
        </div>
      </div>
  </div>
</template>

<script>
import axios from 'axios'
import BookmarkedComponent from '@/components/BookmarkedComponent'

export default {
  name: 'Results',
  props: ['term'],
  data () {
    return {
      results: [],
      noResults: true,
      bookmarks: [],
      bookmarkedIcon: '/src/assets/bookmarked.png',
      bookmarkIcon: '/src/assets/bookmark.png'
    }
  },

  components: {
    BookmarkedComponent
  },
  methods: {
    updateTerm: function (term) {
      const vm = this
      if (term === '' || term === ' ') {
        vm.results = []
        vm.noResults = true
      } else {
        let url = 'https://gateway.marvel.com:443/v1/public/characters?&orderBy=name&limit=12&apikey=11100856935dba0b2e02f59c2253125f&nameStartsWith=' + term
        axios.get(url)
          .then(function (response) {
            let responseData = response.data.data.results
            vm.results = responseData

            if (responseData.length > 0) {
              vm.noResults = false
            }
          })
          .catch(function (error) {
            console.log(error)
          })
      }
    },
    bookmark: function (event, characterID) {
      event.target.src = this.bookmarkedIcon
      let bookmarked = localStorage.getItem('bookmarked')
      if (bookmarked === null || bookmarked === '') {
        bookmarked = []
      } else {
        bookmarked = bookmarked.split(',')
      }

      let wasInArray = (bookmarked.indexOf(characterID.toString()) > -1)

      if (wasInArray) {
        bookmarked = bookmarked.filter((bookmark) => {
          return bookmark.toString() !== characterID.toString()
        })
        event.target.src = this.bookmarkIcon
      } else {
        bookmarked.push(characterID.toString())
      }
      localStorage.setItem('bookmarked', bookmarked.join())
      this.bookmarks = bookmarked
    },
    getIcon: function (characterID) {
      let bookmarked = localStorage.getItem('bookmarked')
      if (bookmarked) {
        let isInArray = (bookmarked.indexOf(characterID.toString()) > -1)

        if (isInArray) {
          return this.bookmarkedIcon
        } else {
          return this.bookmarkIcon
        }
      } else {
        return this.bookmarkIcon
      }
    },
    getTitle: function (characterID) {
      let bookmarked = localStorage.getItem('bookmarked')

      let isInArray = (bookmarked.indexOf(characterID.toString()) > -1)

      if (isInArray) {
        return 'Unbookmark'
      } else {
        return 'bookmark'
      }
    }
  },
  watch: {
    term: function (val) {
      this.updateTerm(val)
    }
  }
}
</script>
