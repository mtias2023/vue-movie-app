<template>
  <div id="app">
    <div class="container-fluid bg-dark text-light py-4">
      <div class="container"> 
        <h1 class="text-center mb-4">Cine Max</h1> 
        <div v-if="mensajeAlerta" :class="[mensajeAlerta.tipo]" role="alert">
          {{ mensajeAlerta.texto }}
        </div>       
        <!-- Barra de Búsqueda -->
        <div class="mb-4">
          <input type="text" v-model="consulta" class="form-control" placeholder="Buscar películas...">
          <button @click="buscarPeliculas" class="btn btn-primary mt-2">Buscar</button>
        </div>

        <!-- Filtrar por Género -->
        <div class="mb-4">
          <select v-model="generoSeleccionado" class="form-select">
            <option value="">Todos los Géneros</option>
            <option v-for="genero in generos" :key="genero.id" :value="genero.id">{{ genero.name }}</option>
          </select>
          <button @click="filtrarPorGenero" class="btn btn-primary mt-2">Filtrar por Género</button>
        </div>

        <!-- Mensaje de Error de Búsqueda -->
        <div v-if="mensajeError" class="alert alert-warning" role="alert">
          {{ mensajeError }}
        </div>

        <!-- Películas Populares -->
        <h2 v-if="mostrarPeliculasPopulares">Películas Populares</h2>
        <div class="row" v-if="mostrarPeliculasPopulares">
          <div class="col-lg-4 col-md-6 col-sm-12 mb-3" v-for="pelicula in peliculasPopulares.slice(0, 6)" :key="pelicula.id">
            <div class="card card-pelicula" v-if="pelicula.poster_path && pelicula.overview">
              <img :src="'https://image.tmdb.org/t/p/w300_and_h450_bestv2' + pelicula.poster_path" class="card-img-top" alt="...">
              <div class="card-body">
                <h5 class="card-title">{{ pelicula.title }}</h5>
                <p class="card-text">{{ pelicula.overview }}</p>
                <button @click="obtenerDetallesPelicula(pelicula.id)" class="btn btn-primary">Detalles</button>
                <button v-if="!esFavorita(pelicula.id)" @click="agregarAFavoritos(pelicula)" class="btn btn-success">Agregar a Favoritos</button>
                <button v-else @click="eliminarDeFavoritos(pelicula.id)" class="btn btn-danger">Eliminar de Favoritos</button>
              </div>
            </div>
            <div v-else class="alert alert-warning" role="alert">
              Datos incompletos para la película: {{ pelicula.title }}
            </div>
          </div>
        </div>

        <!-- Lista de Películas -->
        <h2 v-else>Resultados de Búsqueda</h2>
        <div class="row">
          <div class="col-lg-4 col-md-6 col-sm-12 mb-3" v-for="pelicula in peliculas" :key="pelicula.id">
            <div class="card card-pelicula" v-if="pelicula.poster_path && pelicula.overview">
              <img :src="'https://image.tmdb.org/t/p/w300_and_h450_bestv2' + pelicula.poster_path" class="card-img-top" alt="...">
              <div class="card-body">
                <h5 class="card-title">{{ pelicula.title }}</h5>
                <p class="card-text">{{ pelicula.overview }}</p>
                <button @click="obtenerDetallesPelicula(pelicula.id)" class="btn btn-primary">Detalles</button>
                <button v-if="!esFavorita(pelicula.id)" @click="agregarAFavoritos(pelicula)" class="btn btn-success">Agregar a Favoritos</button>
                <button v-else @click="eliminarDeFavoritos(pelicula.id)" class="btn btn-danger">Eliminar de Favoritos</button>
              </div>
            </div>
            <div v-else class="alert alert-warning" role="alert">
              Datos incompletos para la película: {{ pelicula.title }}
            </div>
          </div>
        </div>

        <!-- Detalles de la Película -->
        <div v-if="peliculaSeleccionada" class="mt-5">
          <div class="card card-detalles">
            <div class="card-body">
              <h2 class="card-title">{{ peliculaSeleccionada.title }}</h2>
              <p class="card-text"><strong>Fecha de Estreno:</strong> {{ peliculaSeleccionada.release_date }}</p>
              <p class="card-text"><strong>Descripción:</strong> {{ peliculaSeleccionada.overview }}</p>
              <img v-if="peliculaSeleccionada.poster_path" :src="'https://image.tmdb.org/t/p/w300_and_h450_bestv2' + peliculaSeleccionada.poster_path" alt="Póster de la Película" class="img-fluid">
              <button v-if="!esFavorita(peliculaSeleccionada.id)" @click="agregarAFavoritos(peliculaSeleccionada)" class="btn btn-success mt-3">Agregar a Favoritos</button>
              <button v-else @click="eliminarDeFavoritos(peliculaSeleccionada.id)" class="btn btn-danger mt-3">Eliminar de Favoritos</button>
            </div>
          </div>
        </div>

        <!-- Favoritos -->
        <div class="mt-5">
          <h2>Mis Favoritos</h2>
          <div class="row">
            <div class="col-lg-4 col-md-6 col-sm-12 mb-3 pb-5" v-for="fav in favoritos" :key="fav.id">
              <div class="card card-pelicula mb-3" v-if="fav.poster_path && fav.overview">
                <img :src="'https://image.tmdb.org/t/p/w300_and_h450_bestv2' + fav.poster_path" class="card-img-top" alt="...">
                <div class="card-body">
                  <h5 class="card-title">{{ fav.title }}</h5>
                  <p class="card-text">{{ fav.overview }}</p>
                  <button @click="eliminarDeFavoritos(fav.id)" class="btn btn-danger">Eliminar</button>
                </div>
              </div>
              <div v-else class="alert alert-warning" role="alert">
                Datos incompletos para la película: {{ fav.title }}
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// app de busqueda de peliculas

const apiKey = '4bca1acc7fc125e6bd15772c2e0bc964'; //clave de API de TMDb

export default {
  data() {
    return {
      peliculas: [],
      peliculasPopulares: [],
      consulta: '',
      peliculaSeleccionada: null,
      favoritos: [],
      generoSeleccionado: '',
      generos: [
        { id: 28, name: 'Acción' },
        { id: 12, name: 'Aventura' },
        { id: 16, name: 'Animación' },
        { id: 35, name: 'Comedia' },
        { id: 80, name: 'Crimen' },
        { id: 99, name: 'Documental' },
        { id: 18, name: 'Drama' },
        { id: 10751, name: 'Familiar' },
        { id: 14, name: 'Fantasía' },
        { id: 36, name: 'Historia' },
        { id: 27, name: 'Terror' },
        { id: 10402, name: 'Música' },
        { id: 9648, name: 'Misterio' },
        { id: 10749, name: 'Romance' },
        { id: 878, name: 'Ciencia ficción' },
        { id: 10770, name: 'Película de TV' },
        { id: 53, name: 'Suspenso' },
        { id: 10752, name: 'Bélica' },
        { id: 37, name: 'Western' }
      ],
      mostrarPeliculasPopulares: true,
      mensajeError: '',
      mensajeAlerta: null
    };
  },

  methods: {
    async buscarPeliculas() {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/search/movie?query=${this.consulta}&language=es-ES&page=1&api_key=${apiKey}`);
        const data = await response.json();
        this.peliculas = data.results;
        this.mostrarPeliculasPopulares = false;
      } catch (error) {
        console.error('Error al buscar películas:', error);
        this.mensajeError = 'Se produjo un error al buscar películas. Por favor, inténtalo de nuevo más tarde.';
      }
    },

    async obtenerPeliculasPopulares() {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/movie/popular?api_key=${apiKey}&language=es-ES&page=1`);
        const data = await response.json();
        this.peliculasPopulares = data.results;
      } catch (error) {
        console.error('Error al obtener películas populares:', error);
        this.mensajeError = 'Se produjo un error al obtener películas populares. Por favor, inténtalo de nuevo más tarde.';
      }
    },

    async obtenerDetallesPelicula(id) {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/movie/${id}?api_key=${apiKey}&language=es-ES`);
        const data = await response.json();
        this.peliculaSeleccionada = data;
      } catch (error) {
        console.error('Error al obtener detalles de la película:', error);
        this.mensajeError = 'Se produjo un error al obtener detalles de la película. Por favor, inténtalo de nuevo más tarde.';
      }
    },

    async agregarAFavoritos(pelicula) {
      if (!this.esFavorita(pelicula.id)) {
        this.favoritos.push(pelicula);
        localStorage.setItem('favoritos', JSON.stringify(this.favoritos));
        this.mostrarAlerta('Película agregada a favoritos', 'alert-success');
      }
    },

    eliminarDeFavoritos(id) {
      this.favoritos = this.favoritos.filter(pelicula => pelicula.id !== id);
      localStorage.setItem('favoritos', JSON.stringify(this.favoritos));
      this.mostrarAlerta('Película eliminada de favoritos', 'alert-danger');
    },

    esFavorita(id) {
      return this.favoritos.some(pelicula => pelicula.id === id);
    },

    mostrarAlerta(mensaje, tipo) {
      this.mensajeAlerta = { texto: mensaje, tipo };
      setTimeout(() => {
        this.mensajeAlerta = null;
      }, 3000);
    },

    async filtrarPorGenero() {
      try {
        const response = await fetch(`https://api.themoviedb.org/3/discover/movie?api_key=${apiKey}&language=es-ES&sort_by=popularity.desc&with_genres=${this.generoSeleccionado}`);
        const data = await response.json();
        this.peliculas = data.results;
        this.mostrarPeliculasPopulares = false;
      } catch (error) {
        console.error('Error al filtrar películas por género:', error);
        this.mensajeError = 'Se produjo un error al filtrar películas por género. Por favor, inténtalo de nuevo más tarde.';
      }
    },

    async cargarFavoritosDesdeLocalStorage() {
      const favoritos = JSON.parse(localStorage.getItem('favoritos'));
      if (favoritos) {
        this.favoritos = favoritos;
      }
    }
  },

  async mounted() {
    await this.obtenerPeliculasPopulares();
    await this.cargarFavoritosDesdeLocalStorage();
  }
};
</script>

<style>
/* styles.css */

body {
  font-family: Arial, sans-serif;
  background-color: #f8f9fa;
  color: #343a40;
  padding-top: 20px;
}

.container {
  max-width: 1200px;
  margin: 0 auto;
}

.card-pelicula {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
}

.card-pelicula:hover {
  transform: translateY(-5px);
}

.card-pelicula img {
  border-top-left-radius: 8px;
  border-top-right-radius: 8px;
}

.card-detalles {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  max-width: 600px;
  margin: 0 auto;
}

.card-detalles img {
  border-radius: 8px;
  max-width: 100%;
}

.btn {
  margin-top: 5px;
  margin: 5px;
}

/* Estilos Responsivos */

@media (max-width: 768px) {
  .card-pelicula {
    width: 100%;
  }
}
</style>



