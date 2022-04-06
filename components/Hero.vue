<template>
  <div class="hero">
      <img :src="trendingMovie.backdrop" alt="Movie Poster">
      <div class="text-container">
          <div class="text">
              <span class="mini-heading">
                  Now Trending
              </span>
              <h1>{{ trendingMovie.title || '-' }}</h1>
              <NuxtLink 
              class="button"
              :to="{name: 'movies-movieid', params: {movieid: trendingMovie.id}}"
            >
              More Details
            </NuxtLink>
          </div>
      </div>
  </div>
</template>

<script>
import axios from 'axios'

export default {
    name: 'Hero',
    data() {
      return {
        trendingMovies: [],
        trendingMovie: {}
      }
    },
    async fetch() {
      await this.getTrendingPoster()
    },
    methods: {
      async getTrendingPoster() {
        const data = axios.get(
          `https://api.themoviedb.org/3/trending/all/week?api_key=0a79a4a744c4ed130fed8f220abf8b73&media_type=all&page=1`
        )
        const result = await data
        this.trendingMovies = result.data.results
        this.getTrendingMovie()
      },
      getTrendingMovie() {
        const randomIndex = Math.floor(Math.random() * 19)
        const data = this.trendingMovies[randomIndex]
        if (this.trendingMovies.length !== 0) {
          this.trendingMovie = {
            backdrop: `https://image.tmdb.org/t/p/original/${data.backdrop_path}`,
            title: data.original_title,
            id: data.id
          }
        }
      }
    }
}
</script>

<style lang="scss" scoped>
.hero {
  height: 400px;
  position: relative;
  @media (min-width: 750px) {
    height: 500px;
  }
  &::after {
    content: '';
    position: absolute;
    top: 0;
    left: 0;
    height: 100%;
    width: 100%;
    background-color: rgba(0, 0, 0, 0.6);
  }
  img {
    width: 100%;
    height: 100%;
    object-fit: cover;
  }
  .text-container {
    z-index: 99;
    position: absolute;
    top: 0;
    margin: 0;
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    justify-content: center;
    .text {
      padding: 0 16px;
      width: 100%;
      max-width: 1400px;
      margin: 0 auto;
    }
    .mini-heading {
      font-weight: 600;
      font-size: 18px;
      text-transform: uppercase;
      color: #c92502;
      margin-bottom: 8px;
      @media (min-width: 750px) {
        font-size: 22px;
      }
    }
    h1 {
      color: #fff;
      font-size: 64px;
      font-weight: 200;
      margin-bottom: 8px;
      @media (min-width: 750px) {
        font-size: 84px;
      }
      span {
        font-weight: 500;
      }
    }
    .button {
      font-size: 20px;
      align-self: flex-start;
    }
  }
}
</style>