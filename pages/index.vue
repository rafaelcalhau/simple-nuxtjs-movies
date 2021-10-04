<template>
  <div class="home">
    <!-- Hero -->
    <Hero />

    <!-- Search -->
    <div class="container search">
      <input v-model.lazy="searchInput" placeholder="Search" type="text" @keyup.enter="$fetch">
      <button v-show="searchInput !== ''" class="button" @click="clearSearch">Clear Search</button>
    </div>

    <!-- Hero -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div class="container movies">
      <!-- Now Streaming -->
      <MoviesList v-if="searchInput === ''" :movies="movies" />

      <!-- Searched Movies -->
      <MoviesList v-else :movies="searchedMovies" />
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      movies: [],
      searchInput: '',
      searchedMovies: [],
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
    } else {
      await this.searchMovies()
    }
  },
  fetchDelay: 600,
  head () {
    return {
      title: 'Simple NuxtJS Movies WebApp - Latest Streaming Movie Info',
      meta: [{
        hid: 'description',
        name: 'description',
        content: 'Get all the latest streaming movies in theaters & online'
      }, {
        hid: 'keywords',
        name: 'keywords',
        content: 'movies, stream, streaming'
      }]
    }
  },
  methods: {
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
    },
    async getMovies() {
      const { data } = await this.$axios.get(
        `movie/now_playing?api_key=${process.env.API_KEY}&language=en-US&page=1`
      )
      this.movies = [...data.results]
    },
    async searchMovies () {
      const { data } = await this.$axios.get(
        `search/movie?api_key=${process.env.API_KEY}&language=en-US&page=1&query=${this.searchInput}`
      )
      this.searchedMovies = [...data.results]
    }
  }
}
</script>

<style lang="scss">
.home {
  .loading {
    padding-top: 120px;
    align-items: flex-start;
  }

  .search {
    display: flex;
    padding: 32px 16px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 6px;
      font-size: 14px;
      border: none;
      &:focus {
        outline: none;
      }
    }
    .button {
      border-top-left-radius: 0;
      border-bottom-left-radius: 0;
    }
  }

  .movies {
    padding: 32px 16px;

    .movies-grid {
      display: grid;
      column-gap: 32px;
      row-gap: 64px;
      grid-template-columns: 1fr;
      @media (min-width: 500px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 750px) {
        grid-template-columns: repeat(2, 1fr);
      }
      @media (min-width: 1100px) {
        grid-template-columns: repeat(4, 1fr);
      }

      .movie {
        position: relative;
        display: flex;
        flex-direction: column;

        .movie-img {
          position: relative;
          overflow: hidden;
          &:hover {
            .overview {
              transform: translateY(0);
            }
          }
          img {
            display: block;
            width: 100%;
            height: 100%;
          }
          .review {
            position: absolute;
            top: 0;
            left: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            width: 40px;
            height: 40px;
            background-color: #c92502;
            color: #fff;
            border-radius: 0 0 16px 0;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1),
              0 2px 4px -1px rgba(0, 0, 0, 0.06);
          }
          .overview {
            line-height: 1.5;
            position: absolute;
            bottom: 0;
            background-color: rgba(201, 38, 2, 0.9);
            padding: 12px;
            color: #fff;
            transform: translateY(100%);
            transition: 0.3s ease-in-out all;
          }
        }

        .info {
          margin-top: auto;
          .title {
            margin-top: 8px;
            color: #fff;
            font-size: 20px;
          }
          .release {
            margin-top: 8px;
            color: #c9c9c9;
          }
          .button {
            margin-top: 8px;
          }
        }
      }
    }
  }
}
</style>