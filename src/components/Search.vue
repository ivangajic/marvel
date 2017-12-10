<template>
  <div class="Search">
      <input type="text" class="characterName" v-model="term" v-on:keyup="searchTermChanged" placeholder="Enter your favorite MARVEL character name!">
  </div>
</template>

<script>
export default {
  name: 'Search',
  data () {
    return {
      term: ''
    }
  },
  methods: {
    searchTermChanged: function (event) {
      this.term = event.target.value
      this.$emit('searchTermChanged', event.target.value)
    }
  }
}
</script>
