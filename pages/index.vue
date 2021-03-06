<template>
  <div>
    <!-- Hero -->
    <Hero />

    <!-- Search -->
    <div class="container search">
      <input
        @keyup.enter="$fetch"
        type="text"
        name="searchInput"
        id="searchInput"
        v-model.lazy="searchInput"
        placeholder="Search..."
      />
      <button @click="clearSearch" v-show="searchInput !== ''" class="button">
        Clear search
      </button>
    </div>

    <!-- Loading  -->
    <Loading v-if="$fetchState.pending" />

    <!-- Movies -->
    <div v-else class="container movies">
      <!-- Searched movies -->
      <div v-if="searchInput !== ''" id="movie-grid" class="movies-grid">
        <div
          class="movie"
          v-for="(movie, index) in searchedMovies"
          :key="index"
        >
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`"
              alt=""
            />
            <div class="review">{{ movie.vote_average }}</div>
            <div class="overview">{{ movie.overview }}</div>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{ new Date(movie.release_date).toLocaleDateString("pt-br") }}
            </p>
            <nuxt-link
              class="button button-light"
              :to="{ name: 'movies-id', params: { id: movie.id } }"
              >Get More Info</nuxt-link
            >
          </div>
        </div>
      </div>

      <!-- Now Playing  -->
      <div v-else id="movie-grid" class="movies-grid">
        <div class="movie" v-for="(movie, index) in movies" :key="index">
          <div class="movie-img">
            <img
              :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`"
              alt=""
            />
            <div class="review">{{ movie.vote_average }}</div>
            <div class="overview">{{ movie.overview }}</div>
          </div>
          <div class="info">
            <p class="title">
              {{ movie.title.slice(0, 25) }}
              <span v-if="movie.title.length > 25">...</span>
            </p>
            <p class="release">
              Released:
              {{ new Date(movie.release_date).toLocaleDateString("pt-br") }}
            </p>
            <nuxt-link
              class="button button-light"
              :to="{ name: 'movies-id', params: { id: movie.id } }"
              >Get More Info</nuxt-link
            >
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Ultimos Lançamentos",
  head() {
    return {
      title: "Últimos Lançamentos",
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
      movies: [],
      searchedMovies: [],
      searchInput: "",
    };
  },

  async fetch() {
    if (this.searchInput === "") {
      await this.getMovies();
      return;
    }
    await this.searchMovies();
  },
  fetchDelay: 1000, //sets the delay of the fetch request to 1sec, this may improve ux

  methods: {
    async getMovies() {
      const data = this.$axios.get(
        "https://api.themoviedb.org/3/movie/now_playing?api_key=da2bc268e07d9b4f763d9274e370a55a&language=pt-BR"
      );
      const res = await data;

      res.data.results.forEach((movie) => {
        this.movies.push(movie);
      });
      console.log("hi");
    },
    async searchMovies() {
      const data = this.$axios.get(
        `https://api.themoviedb.org/3/search/movie?api_key=da2bc268e07d9b4f763d9274e370a55a&language=pt-BR&query=${this.searchInput}`
      );
      const res = await data;

      res.data.results.forEach((movie) => {
        this.searchedMovies.push(movie);
      });
      console.log(this.searchedMovies);
    },
    clearSearch() {
      this.searchedMovies = [];
      this.searchInput = "";
    },
  },
};
</script>

<style lang="scss">
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
</style>
