<template>
  <div class="container-fluid bg-dark text-light py-4">
    <div class="container"> 
      <h1 class="text-center mb-4"> <img src="@/assets/img/logo.png" alt="logo">Cine Max</h1> 
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
        <h2>Mis Favoritos <img src="@/assets/img/footer.png" alt="estrella"></h2>
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
</template>

<script>
export default {
  data() {
    return {
      consulta: '',
      mensajeAlerta: null,
      generos: [],
      generoSeleccionado: '',
      mensajeError: '',
      mostrarPeliculasPopulares: true,
      peliculasPopulares: [],
      peliculas: [],
      peliculaSeleccionada: null,
      favoritos: []
    }
  },
  methods: {
    buscarPeliculas() {
      // Implementación de la función para buscar películas
    },
    filtrarPorGenero() {
      // Implementación de la función para filtrar por género
      

    },
    obtenerDetallesPelicula(id) {
      // Implementación de la función para obtener detalles de una película
    },
    agregarAFavoritos(pelicula) {
      // Implementación de la función para agregar a favoritos
    },
    eliminarDeFavoritos(id) {
      // Implementación de la función para eliminar de favoritos
    },
    esFavorita(id) {
      // Implementación de la función para verificar si una película es favorita
      return this.favoritos.some(fav => fav.id === id);
    }
  },
  mounted() {
    // Implementación de la lógica para obtener las películas populares y los géneros al montar el componente
  }
}
</script>

<style scoped>
/* Tus estilos CSS personalizados aquí */
@import "@/assets/css/styles.css";

/* styles.css */

body {
  font-family: Arial, sans-serif;
  background-color: #f8f9fa;
  color: #343a40;
  padding-top: 20px;
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
.btn{
  margin-top: 5px;
  margin: 5px;
}
/* Estilos Responsivos */

.alert-success {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  width: 80%;
  max-width: 600px;
  padding: 1rem;
  border-radius: 0.5rem;
  background-color: #d4edda;
  border-color: #c3e6cb;
  color: #155724;
  }

.alert-danger {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  width: 80%;
  max-width: 600px;
  padding: 1rem;
  border-radius: 0.5rem;
  background-color: #f8d7da;
  border-color: #f5c6cb;
  color: #721c24;
  }
img{
  width: 50px;
  height: 50px;
  margin-left: 6px;
}

/*responsive*/
@media (max-width: 768px) {
  .card-pelicula {
    width: 100%;
  }
  
}


</style>

