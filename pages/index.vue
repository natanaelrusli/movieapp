/* eslint-disable no-console */
<template>
  <div class="home">
    <!-- Hero Banner -->
    <Hero />

    <!-- Search -->
    <div class="container search">
      <input v-model.lazy="searchInput" type="text" style="width: 200px" placeholder="Search" @keyup.enter="$fetch" />
      <button v-show="searchInput !== ''" class="button" @click="clearSearch">Clear Search</button>
    </div>

    <!-- Loading -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div v-else class="container movies">
      <!-- Now Streaming -->
      <div v-if="searchInput === ''" id="movies-grid" class="movies-grid">
        <div v-for="(movie, index) in movies" :key="index" class="movie">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="Movie Poster">
            <p class="review">{{ movie.vote_average }}/10</p>
            <p class="overview">{{ movie.overview.slice(0, 200)}}<span v-if="movie.overview.length > 200">...</span></p>
          </div>
          <div class="info">
            <!-- How you create elipsis -->
            <p class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric'
                })
              }}
            </p>
            <NuxtLink 
              class="button button-light"
              :to="{name: 'movies-movieid', params: {movieid: movie.id}}"
            >
              Get More Info
            </NuxtLink>
          </div>
        </div>
      </div>
      <!-- Searched Movies  -->
      <div v-else-if="searchInput !== ''" id="movies-grid" class="movies-grid">
        <div v-for="(movie, index) in searchedMovies" :key="index" class="movie">
          <div class="movie-img">
            <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="Movie Poster">
            <p class="review">{{ movie.vote_average }}/10</p>
            <p class="overview">{{ movie.overview.slice(0, 300)}}<span v-if="movie.overview.length > 70"> ...</span></p>
          </div>
          <div class="info">
            <!-- How you create elipsis -->
            <p class="title">{{movie.title.slice(0,25)}} <span v-if="movie.title.length > 25">...</span></p>
            <p class="release">
              Released:
              {{
                new Date(movie.release_date).toLocaleString('en-us', {
                  month: 'long',
                  day: 'numeric',
                  year: 'numeric'
                })
              }}
            </p>
            <NuxtLink 
              class="button button-light"
              :to="{name: 'movies-movieid', params: {movieid: movie.id}}"
            >
              Get More Info
            </NuxtLink>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
  name: 'IndexPage',
  data() {
    return {
      movies: [],
      searchedMovies: [],
      searchInput: ''
    }
  },
  async fetch() {
    if (this.searchInput === '') {
      await this.getMovies()
      return
    }
    await this.searchMovies()
  },
  head() {
    return {
      title: 'Nuxtflix - Latest Movie Info',
      meta: [
        //  If the want to set the description from within a child component we need to use the same hid value
        {
          hid: 'description',
          name: 'description',
          content: 'Get all the latest streaming movies in theaters & online'
        },
        {
          hid: 'keywords',
          name: 'keywords',
          content: 'movies, stream, streaming'
        }
      ]
    }
  },
  methods: {
    async getMovies() {
      const data = axios.get(
        `https://api.themoviedb.org/3/movie/now_playing?api_key=0a79a4a744c4ed130fed8f220abf8b73&language=en-US&page=1`
      )
      const result = await data
      result.data.results.forEach(movie => {
        this.movies.push(movie)
      })
    },
    async searchMovies() {
      this.searchedMovies = []
      const data = axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=0a79a4a744c4ed130fed8f220abf8b73&language=en-US&page=1&query=${this.searchInput}`
      )
      const result = await data
      result.data.results.forEach(movie => {
        this.searchedMovies.push(movie)
      })
      // eslint-disable-next-line no-console
      console.log(this.searchedMovies)
    },
    clearSearch() {
      this.searchInput = ''
      this.searchedMovies = []
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
    padding: 32px 6px;
    input {
      max-width: 350px;
      width: 100%;
      padding: 12px 20px;
      font-size: 14px;
      border-radius: 5px;
      border: none;
      color: #949494;
      border: 2px solid #fff;
      &:focus {
        outline: none;
        border: 2px solid #c92502;
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
        grid-template-columns: repeat(3, 1fr);
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
            width: 55px;
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
            color: #949494;
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
