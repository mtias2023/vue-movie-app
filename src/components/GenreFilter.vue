<template>
  <div class="mb-4">
    <select v-model="generoSeleccionado" class="form-select">
      <option value="">Todos los Géneros</option>
      <option v-for="genero in generos" :key="genero.id" :value="genero.id">{{ genero.name }}</option>
    </select>
    <button @click="filtrarPorGenero" class="btn btn-primary mt-2">Filtrar por Género</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
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
      ]
    };
  },
  methods: {
    filtrarPorGenero() {
      this.$emit('filtrar-por-genero', this.generoSeleccionado);
    }
  }
};
</script>

<style scoped>
/* Estilos específicos para GenreFilter.vue */
</style>
