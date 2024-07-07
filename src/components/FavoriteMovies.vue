<template>
  <div class="mt-5">
    <h2>Mis Favoritos <img src="../assets/footer.png" alt="estrella"></h2>
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
</template>

<script>
export default {
  name: 'FavoriteMovies',
  props: {
    favoritos: {
      type: Array,
      required: true
    }
  },
  methods: {
    eliminarDeFavoritos(id) {
      this.$emit('eliminar', id); // Emitir evento al componente padre
    }
  }
};
</script>

<style scoped>
/* Estilos específicos del componente */
</style>
