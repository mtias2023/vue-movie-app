<template>
  <div v-if="peliculaSeleccionada" class="mt-5">
    <div class="card card-detalles">
      <div class="card-body">
        <h2 class="card-title">{{ peliculaSeleccionada.title }}</h2>
        <p class="card-text"><strong>Fecha de Estreno:</strong> {{ peliculaSeleccionada.release_date }}</p>
        <p class="card-text"><strong>Descripción:</strong> {{ peliculaSeleccionada.overview }}</p>
        <p class="card-text"><strong>Puntuación:</strong> {{ peliculaSeleccionada.vote_average }}</p>
        <img :src="'https://image.tmdb.org/t/p/w500' + peliculaSeleccionada.poster_path" alt="poster" class="img-fluid">
        <button v-if="!esFavorita(peliculaSeleccionada.id)" @click="agregarAFavoritos(peliculaSeleccionada)" class="btn btn-success mt-3">Agregar a Favoritos</button>
        <button v-else @click="eliminarDeFavoritos(peliculaSeleccionada.id)" class="btn btn-danger mt-3">Eliminar de Favoritos</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    peliculaSeleccionada: {
      type: Object,
      required: true
    }
  },
  methods: {
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
/* Estilos específicos para MovieDetails.vue */
.card-detalles {
  max-width: 600px;
  margin: 0 auto;
}
</style>
