<template>
  <div>
    <h2>Películas Populares</h2>
    <div class="row">
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
  </div>
</template>

<script>
export default {
  props: {
    peliculasPopulares: {
      type: Array,
      required: true
    }
  },
  methods: {
    obtenerDetallesPelicula(id) {
      this.$emit('obtener-detalles', id);
    },
    agregarAFavoritos(pelicula) {
      this.$emit('agregar-favorito', pelicula);
    },
    eliminarDeFavoritos(id) {
      this.$emit('eliminar-favorito', id);
    },
    esFavorita(id) {
      return this.$emit('es-favorita', id);
    }
  }
};
</script>

<style scoped>
/* Estilos específicos para PopularMovies.vue */
</style>
