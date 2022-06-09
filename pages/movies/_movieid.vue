<template>
    <div>
        <Loading v-if="$fetchState.pending" />  
        <div v-else class="container single-movie">
            <NuxtLink class="button" :to="{ name: 'index' }">Back</NuxtLink>
            <div class="movie-info">
                <div class="movie-img">
                    <img :src="`https://image.tmdb.org/t/p/w500/${movie.poster_path}`" alt="Movie Poster">
                </div>
                <div class="movie-content">
                    <h1>{{ movie.title }}</h1>
                    <p class="movie-status" :class="{ released : isReleased }">{{ movie.status }}</p>
                    <p v-if="movie.tagline" class="movie-fact tagline">
                        <span>TagLine:</span> {{ movie.tagline }}
                    </p>
                    <p class="movie-fact">
                        <span>Released</span>
                        Released:
                        {{
                            movie.release_date ?
                            new Date(movie.release_date).toLocaleString('en-us', {
                              month: 'long',
                              day: 'numeric',
                              year: 'numeric'
                            })
                            : '-'
                        }}
                    </p>
                    <p class="movie-fact">
                        <span>Duration:</span> {{ movie.runtime }} minutes
                    </p>
                    <p class="movie-fact">
                        <span>Revenue:</span>
                        {{
                            movie.revenue.toLocaleString('en-us', {
                              style: 'currency',
                              currency: 'USD',
                            })
                        }}
                    </p>
                    <p class="movie-fact"><span>Overview:</span> {{ movie.overview }}</p>
                    <a-tooltip placement="topLeft">
                      <template slot="title">
                        <span>{{ `Go to ${movie.title} IMDB page` }}</span>
                      </template>
                      <a v-if="movie.imdb_id" class="button" target="_blank" :href="`https://www.imdb.com/title/${movie.imdb_id}`">Go to IMDB</a>
                    </a-tooltip>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'SingleMovie',
    data() {
        return {
            movie: ''
        }
    },
    async fetch() {
        await this.getSingleMovie()
    },
    head() {
        return {
            title: `${this.movie.title} - Nuxtflix`
        }
    },
    computed: {
      isReleased() {
        return this.movie.status === 'Released'
      }
    },
    methods: {
        async getSingleMovie() {
            const data = axios.get(
                `https://api.themoviedb.org/3/movie/${this.$route.params.movieid}?api_key=0a79a4a744c4ed130fed8f220abf8b73&language=en-US`
            )
            const result = await data
            this.movie = result.data
        }
    }
}
</script>

<style lang="scss" scoped>
.single-movie {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    height: 40px;
    margin-bottom: 32px;
    margin-top: 32px;
  }
  .movie-info {
    display: flex;
    flex-direction: column;
    align-items: center;
    gap: 32px;
    color: #fff;
    @media (min-width: 800px) {
      flex-direction: row;
      align-items: flex-start;
    }
    .movie-img {
      img {
        max-height: 500px;
        width: 100%;
        @media (min-width: 800px) {
          max-height: 700px;
          width: initial;
        }
      }
    }
    .movie-content {
      h1 {
        font-size: 56px;
        font-weight: 400;
        color: #fff;
      }
      .movie-status {
        background-color: red;
        width: fit-content;
        padding: 7px;
        border-radius: 7px;
      }
      .released {
        background-color: green;    
      }
      .movie-fact {
        margin-top: 12px;
        font-size: 20px;
        line-height: 1.5;
        span {
          font-weight: 600;
          text-decoration: underline;
        }
      }
      .tagline {
        font-style: italic;
        span {
          font-style: normal;
        }
      }
    }
  }
}
</style>