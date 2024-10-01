<template>
  <div class="home">
    <h1>Movie Explorer</h1>
    <div class="search-filter-container">
      <input
        v-model="searchQuery"
        @input="debouncedSearch"
        placeholder="Search movies..."
        class="search-input"
      />
      <select v-model="sortBy" @change="handleSortChange" class="filter-select">
        <option value="popularity.desc">Popularity (Descending)</option>
        <option value="popularity.asc">Popularity (Ascending)</option>
        <option value="vote_average.desc">Rating (Descending)</option>
        <option value="vote_average.asc">Rating (Ascending)</option>
        <option value="release_date.desc">Release Date (Descending)</option>
        <option value="release_date.asc">Release Date (Ascending)</option>
      </select>
      <input
        v-model="year"
        @input="debouncedSearch"
        placeholder="Filter by year"
        class="year-input"
        type="number"
      />
    </div>
    <Suspense>
      <template #default>
        <MovieList :movies="movies" />
      </template>
      <template #fallback>
        <div class="loading">Loading...</div>
      </template>
    </Suspense>
    <div v-if="error" class="error">{{ error }}</div>
  </div>
</template>

<script setup>
import { ref, computed, watch } from "vue";
import MovieList from "../components/MovieList.vue";
import { useMovies } from "../composables/useMovies";

const searchQuery = ref("");
const sortBy = ref("popularity.desc");
const year = ref("");

const { movies, error, fetchMovies } = useMovies();

const params = computed(() => ({
  sort_by: sortBy.value,
  year: year.value,
  query: searchQuery.value,
}));

let timeout = null;

function debouncedSearch() {
  if (timeout) {
    clearTimeout(timeout);
  }
  timeout = setTimeout(() => {
    fetchMovies(params.value);
  }, 300);
}

function handleSortChange() {
  fetchMovies(params.value);
}

watch([sortBy], () => {
  fetchMovies(params.value);
});

// Initial fetch
fetchMovies(params.value);
</script>

<style scoped>
.home {
  max-width: 100%;
  margin: 0 auto;
  padding: 1rem;
}

.search-filter-container {
  display: flex;
  flex-direction: column;
  gap: 1rem;
  margin-bottom: 1rem;
}

.search-input,
.filter-select,
.year-input {
  width: 100%;
  padding: 0.5rem;
  border: 1px solid #ccc;
  border-radius: 4px;
  font-size: 1rem;
}

.error {
  color: red;
  margin-top: 1rem;
}

@media (min-width: 768px) {
  .home {
    padding: 2rem;
  }

  .search-filter-container {
    flex-direction: row;
    align-items: center;
  }

  .search-input {
    flex-grow: 1;
  }

  .filter-select,
  .year-input {
    width: auto;
  }
}
</style>
