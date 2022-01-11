<template>
  <div class="container single-movie">
    <nuxt-link class="button" to="/">Back</nuxt-link>
    <img :src="`https://image.tmdb.org/t/p/w500${movie.poster_path}`" alt="" />
    <p>{{ movie.title }}</p>
  </div>
</template>

<script>
export default {
  name: "single-movie",

  data() {
    return {
      movie: {},
    };
  },

  async fetch() {
    await this.getSingleMovie();
  },

  methods: {
    async getSingleMovie() {
      const data = this.$axios.get(
        `https://api.themoviedb.org/3/movie/${this.$route.params.id}?api_key=da2bc268e07d9b4f763d9274e370a55a&language=pt-BR`
      );
      const res = await data;
      console.log(res.data);
      this.movie = res.data;
    },
  },
};
</script>

<style lang="scss" scoped>
</style>
