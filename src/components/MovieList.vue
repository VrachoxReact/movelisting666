<template>
  <div class="movie-list">
    <MovieCard v-for="movie in movies" :key="movie.id" :movie="movie" />
  </div>
</template>

<script setup>
import { defineProps } from "vue";
import MovieCard from "./MovieCard.vue";

defineProps({
  movies: {
    type: Array,
    required: true,
  },
});
</script>

<style scoped>
.movie-list {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(140px, 1fr));
  gap: 1rem;
}

@media (min-width: 480px) {
  .movie-list {
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
  }
}

@media (min-width: 768px) {
  .movie-list {
    grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
    gap: 1.5rem;
  }
}
</style>
