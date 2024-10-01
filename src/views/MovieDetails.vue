<template>
  <div v-if="movie" class="movie-details">
    <h1>{{ movie.title }}</h1>
    <div class="movie-info">
      <img :src="posterUrl" :alt="movie.title" class="movie-poster" />
      <div class="movie-text">
        <p><strong>Release Date:</strong> {{ movie.release_date }}</p>
        <p><strong>Rating:</strong> {{ movie.vote_average }}/10</p>
        <p><strong>Overview:</strong> {{ movie.overview }}</p>
      </div>
    </div>
    <h2>Images</h2>
    <div class="image-gallery">
      <img
        v-for="image in movie.images.backdrops.slice(0, 5)"
        :key="image.file_path"
        :src="getImageUrl(image.file_path)"
        :alt="movie.title"
        class="gallery-image"
      />
    </div>
  </div>
  <div v-else-if="error" class="error">{{ error }}</div>
  <div v-else class="loading">Loading...</div>
</template>

<script setup>
import { ref, onMounted, computed } from "vue";
import { useRoute } from "vue-router";
import tmdbApi from "../services/tmdbApi";

const route = useRoute();
const movie = ref(null);
const error = ref(null);

const posterUrl = computed(() => {
  return movie.value?.poster_path
    ? `https://image.tmdb.org/t/p/w500${movie.value.poster_path}`
    : "https://via.placeholder.com/500x750?text=No+Poster";
});

const getImageUrl = (path) => `https://image.tmdb.org/t/p/w500${path}`;

onMounted(async () => {
  try {
    const response = await tmdbApi.getMovieDetails(route.params.id);
    movie.value = response.data;
  } catch (err) {
    console.error("Error fetching movie details:", err);
    error.value =
      "An error occurred while fetching movie details. Please try again.";
  }
});
</script>

<style scoped>
.movie-details {
  max-width: 100%;
  margin: 0 auto;
  padding: 1rem;
}

.movie-info {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 1rem;
}

.movie-poster {
  width: 100%;
  max-width: 300px;
  height: auto;
  margin: 0 auto;
}

.movie-text {
  flex: 1;
}

.image-gallery {
  display: flex;
  gap: 0.5rem;
  overflow-x: auto;
  padding-bottom: 1rem;
}

.gallery-image {
  width: 200px;
  height: auto;
  flex-shrink: 0;
}

.error,
.loading {
  text-align: center;
  margin-top: 1rem;
  font-size: 1rem;
}

.error {
  color: red;
}

@media (min-width: 768px) {
  .movie-details {
    padding: 2rem;
  }

  .movie-info {
    flex-direction: row;
    gap: 2rem;
  }

  .movie-poster {
    margin: 0;
  }

  .image-gallery {
    gap: 1rem;
  }
}
</style>
