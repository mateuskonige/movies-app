<template>
  <Loading v-if="$fetchState.pending"/>
  <div v-else class="container single-movie">
    <nuxt-link class="button" to="/">Back</nuxt-link>

    <div class="movie-info">
      <div class="movie-img">
        <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" alt="" />
      </div>

      <div class="movie-content">
        <h1>Title: {{ movie.title }}</h1>
        <p class="movie-fact tagline">
          <span>Tagline:</span> {{movie.tagline ? movie.tagline : 'N/A' }}
        </p>
        <p class="movie-fact ">
          <span>Released:</span> {{ new Date(movie.release_date).toLocaleDateString('pt-br') }}
        </p>
        <p class="movie-fact ">
          <span>Duration:</span> {{ movie.runtime }} min
        </p>
        <p class="movie-fact ">
          <span>Revenue:</span> {{ movie.revenue.toLocaleString('en-us', {style: 'currency', currency: 'USD'}) }}
        </p>
        <p class="movie-fact ">
          <span>Overview:</span> {{ movie.overview }}
        </p>

      </div>
    </div>



  </div>
</template>

<script>
export default {
  name: "single-movie",

    head() {
    return {
      title: this.movie.title,
      meta: [
        { hid: 'description',
          name: 'description',
          content: 'Encontre informações para os mais variados lançamentos de filmes no cinema e online!'
        },
        { hid: 'keywords',
          name: 'keywords',
          content: 'movies, streaming'
        },
      ]
    };
  },


  data() {
    return {
      movie: {},
    };
  },

  async fetch() {
    await this.getSingleMovie();
  },
  fetchDelay: 1000,

  methods: {
    async getSingleMovie() {
      const data = this.$axios.get(
        `https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=da2bc268e07d9b4f763d9274e370a55a&language=pt-BR`
      );
      const res = await data;
      this.movie = res.data;
    },
  },
};
</script>

<style lang="scss">
.single-movie {
  color: #fff;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding: 32px 16px;
  .button {
    align-self: flex-start;
    margin-bottom: 32px;
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
