<template>
  <div class="container">

    <movie-search
      @search-term-change="onSearchTermChanged"
      class="mt-4"
    />

    <div class="pt-3">
      <b-badge
        pill
        variant="primary"
        v-if="movies.length"
      >
        Selected: {{ selectedMoviesCounter }}
      </b-badge>


      <movie-row
        v-for="movie in movies"
        :key="movie.id"
        :movie="movie"
        @on-selected-movie="onSelectedMovie"
      />

      <b-alert
        show
        variant="warning"
        v-if="!movies.length"
      >
        No Movies
      </b-alert>
    </div>
  </div>
</template>

<script>
import MoviesService from './../services/MoviesService'
import MovieRow from './MovieRow.vue'
import MovieSearch from './MovieSearch.vue'
import MovieForm from './MovieForm.vue'

export default {
  name: 'AppMovies',
  components: {
    MovieRow,
    MovieSearch,
    MovieForm
  },
  data() {
    return {
      movies: [],
      selectedMoviesIds: []
    }
  },
  methods: {
    storeMovie() {
      MoviesService.store(this.movieForm)
    },
    onSearchTermChanged(term) {
      MoviesService.index(term)
        .then(({ data }) => {
          this.movies = data
        })
    },
    onSelectedMovie(movie) {
      if (this.selectedMoviesIds.indexOf(movie.id) > -1) {
        return;
      }
      this.selectedMoviesIds.push(movie.id)
    }
  },
  computed: {
    selectedMoviesCounter() {
      return this.selectedMoviesIds.length
    }
  },
  beforeRouteEnter(to, from, next) {
    MoviesService.index().then(({ data }) => {
      next((context) => {
        context.movies = data;
      })
    })
  }
}
</script>
