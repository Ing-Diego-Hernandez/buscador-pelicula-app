<template>
  <div id="app">
    <h1>Buscador de Peliculas</h1>
    <input 
      v-model="query" 
      placeholder="Busca una pelicula..." 
      @keyup.enter="searchMovies"
    />
    <button @click="searchMovies">Buscar</button>

    <div v-if="error">{{ error }}</div>

    <div v-if="loading">Loading...</div>

    <div v-if="movies && movies.length > 0">
      <h2>Resultados:</h2>
      <ul>
        <li v-for="movie in movies" :key="movie.imdbID">
          <img :src="movie.Poster" alt="Poster" width="50">
          {{ movie.Title }} ({{ movie.Year }})
        </li>
      </ul>
    </div>

    <div v-if="movies && movies.length === 0">
      <p>No se encontraron peliculas. Trata un nombre diferente</p>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      query: '',
      movies: [],
      error: null,
      loading: false
    };
  },
  methods: {
    async searchMovies() {
      if (!this.query) {
        this.error = "Ingresa el titulo de una pelicula";
        return;
      }

      this.error = null;
      this.loading = true;

      try {
        const response = await axios.get(`https://www.omdbapi.com/?apikey=142f619e&s=${this.query}`);
        if (response.data.Response === "True") {
          this.movies = response.data.Search;
        } else {
          this.error = response.data.Error;
          this.movies = [];
        }
      } catch (err) {
        this.error = "An error occurred while fetching movies.";
      } finally {
        this.loading = false;
      }
    }
  }
};
</script>

<style scoped>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  text-align: center;
  margin-top: 60px;
}

input {
  padding: 10px;
  width: 300px;
  margin-right: 10px;
}

button {
  padding: 10px 15px;
}

ul {
  list-style: none;
  padding: 0;
}

li {
  margin: 10px 0;
  display: flex;
  align-items: center;
}

img {
  margin-right: 10px;
}
</style>
