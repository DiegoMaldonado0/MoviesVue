<template>
  <div id="app">
    <nav>
      <div class="nav-left">
        <img src="../src/assets/diego.png" alt="logo" class="logo">
        <a href="#"><img src="../src/assets/home.png" alt="" class="nav-icon"></a>
        <a href="#"><img src="../src/assets/search.png" alt="" class="nav-icon"></a>
        <a href="#"><img src="../src/assets/plus.png" alt="" class="nav-icon"></a>
        <a href="#"><img src="../src/assets/espn.png" alt="" class="nav-icon"></a>
      </div>
      <div class="nav-right">
        <img src="../src/assets/darth-vader.png" alt="" class="profile">
      </div>
    </nav>

    <input 
      type="text"
      v-model="searchTerm"
      @keyup.enter="handleSearch"
      placeholder="Buscar películas..."
    >

    <main>
      <!-- Resultado de las búsquedas -->
      <div class="movie-results" v-if="movies.length">
        <div v-for="movie in movies" :key="movie.imdbID" class="movie-card" @click="selectMovie(movie.imdbID)">
          <img :src="movie.Poster" alt="Poster">
          <h2>{{ movie.Title }}</h2>
        </div>
      </div>

      <!-- Detalles de la película seleccionada -->
      <transition name="fade">
        <div v-if="selectedMovie" class="movie-details">
          <h2>{{ selectedMovie.Title }}</h2>
          <img :src="selectedMovie.Poster" alt="Poster">
          <p><strong>Año:</strong>{{ selectedMovie.Year }}</p>
          <p><strong>Director:</strong>{{ selectedMovie.Director }}</p>
          <p><strong>Actores:</strong>{{ selectedMovie.Actors }}</p>
          <p><strong>Trama:</strong>{{ selectedMovie.Plot }}</p>

          <p v-if="selectedMovie.Ratings && selectedMovie.Ratings.length">
            <strong>Ratings:</strong>
            <ul>
              <li v-for="rating in selectedMovie.Ratings" :key="rating.Source">
                <strong>{{ rating.Source }}:</strong> {{ rating.Value }}
              </li>
            </ul>
          </p>
          <button @click="goBack">Volver</button>
        </div>
      </transition>
    </main>

    <footer>
      <img src="../src/assets/diego.png" alt="">
      <p>Desarrollado por Diego Maldonado</p>
      <p>El servicio Diego+ es comercializado por el Centro Universitario de Ciencias Exactas e Ingenierias. UdeG.</p>
    </footer>

  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      searchTerm: '',
      movies: [],
      selectedMovie: null
    };
  },
  methods: {
    // Elimina la pelicula seleccionada y realiza un nueva busqueda
    handleSearch() {
      this.selectedMovie = null;
      this.searchMovies();
    },
    // Busqueda de peliculas
    async searchMovies() {
      if (this.searchTerm) {
        const apiKey = '9f154d6a';
        const response = await axios.get(`https://www.omdbapi.com/?s=${this.searchTerm}&apikey=${apiKey}`);
        this.movies = response.data.Search || [];

        // Scroll automatico
        setTimeout(() => {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
      }, 100);
      }
    },
    async selectMovie(imdbID) {
      const apiKey = '9f154d6a';
      const response = await axios.get(`https://www.omdbapi.com/?i=${imdbID}&apikey=${apiKey}`);
      this.selectedMovie = response.data;

      // Scroll automatico
      this.$nextTick(() => {
        const detailsSection = document.querySelector('.movie-details');
        window.scrollTo({
          top: detailsSection.offsetTop - 220,
          behavior: 'smooth'
        });
      });
    },
    goBack(){
      this.selectedMovie = null;
      setTimeout(() => {
        window.scrollTo({
          top: 0,
          behavior: 'smooth'
        });
      }, 100);
      }
  }
};
</script>

<style>
  #app {
    font-family: Arial, sans-serif;
    color: #fff;
    min-height: 100vh;
    padding-top: 80px;
    text-align: center;
    display: flex;
    flex-direction: column;
  }

  body{
    margin: 0;
    padding: 0;
    background-image: url(../src/assets/background.png);
    background-size: cover;
    background-position: center;
    background-repeat: no-repeat;
    background-attachment: fixed;
  }

  main{
    flex: 1;
    display: flex;
    flex-direction: column;
  }

  nav {
    background-color: #000;
    position: fixed;
    width: 100%;
    top: 0;
    left: 0;
    padding: 40px;
    z-index: 1000;
    display: flex;
    justify-content: space-between;
    align-items: center;
  } 

  .nav-left {
    display: flex;
    align-items: center;
  }

  .logo{
    height: 40px;
    object-fit: contain;
    margin-right: 45px;
  }

  .nav-icon {
    height: 30px;
    margin-right: 45px;
    cursor: pointer;
  }

  .nav-icon:hover {
    border-bottom:#fff 2px solid;
  }

  .nav-right {
    display: flex;
    align-items: center;
  }

  .profile {
    height: 40px;
    width: 40px;
    border-radius: 50%;
    object-fit: cover;
    margin-right: 70px;
  }

  .movie-results {
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
    margin: 20px auto;
    margin-top: 80px;
    padding: 20px;
  }

  .movie-card {
    background-color: none;
    display: inline-block;
    border-radius: 10px;
    overflow: hidden;
    width: calc(25% - 30px);
    margin: 15px;
    text-align: center;
    transition: transform 0.3s ease, box-shadow 0.3s ease;
  }

  @media (max-width: 780px) {
    .movie-card {
      width: calc(50% - 30px);
    }
  }

  @media (max-width: 480px) {
    .movie-card {
      width: calc(100% - 30px);
    }
  }

  .movie-card:hover {
    transform: scale(1.05);
    box-shadow: 0 10px 20px rgba(0, 0, 0, 0.5);
    outline: 2px solid #fff;
  }

  .movie-card img {
    width: 100%;
    max-width: 200px;
    height: auto;
    margin-bottom: 1rem;
    border-radius: 10px;
    transition: box-shadow 0.3s ease;
  }

  .movie-card:hover img {
    box-shadow: 0 8px 15px rgba(0, 0, 0, 0.3);
  }

  .movie-card h2 {
    font-size: 18px;
    margin: 10px 0;
  }

  ul{
    list-style: none;
    padding: 0;
  }

  .fade-enter-active, .fade-leave-active {
    transition: opacity 0.5s ease, transform 0.5s ease;
  }

  .fade-enter, .fade-leave-to {
    opacity: 0;
    transform: translateY(20px);
  }

  .fade-enter-to {
    opacity: 1;
    transform: translateY(0);
  }

  input[type="text"] {
    position: fixed;
    background-color: #2d2d2d;
    top: 120px;
    left: 0;
    padding: 35px;
    margin: 0;
    border: 0;
    width: 100%;
    font-size: 28px;
    transition: background-color 0.5s ease;
    box-sizing: border-box;
    color: #fff;
    z-index: 1000;
  }

  input[type="text"]:focus {
    background-color: #3d3d3deb;
    outline: none;
  }

  button {
    background-color: transparent;
    color: #fff;
    padding: 10px 20px;
    border-radius: 5px;
    margin-bottom: 20px;
    border: 0;
  }

  button:hover {
    background-color: #3d3d3deb;
    cursor: pointer;
  }

  footer {
    background-color: #000;
    color: #fff;
    padding: 20px;
    bottom: 0;
    position: relative;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
  }

  footer img {
    height: 40px;
    object-fit: contain;
  }

  footer p {
    font-size: 12px;
    margin: 5px 0;
  }
</style>
