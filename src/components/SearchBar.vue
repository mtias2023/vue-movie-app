<template>
  <div class="navbar">
    <img src="img/logo.png" alt="logo">
    <h1>Cine Max</h1>
  </div>
</template>

<script>
export default {
  // Puedes añadir lógica adicional aquí si es necesario
};
</script>

<style scoped>
/* Estilos específicos para NavBar.vue */
.navbar {
  background-color: #343a40;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 10px 0;
}

.navbar img {
  height: 50px;
  margin-right: 10px;
}

.navbar h1 {
  font-size: 1.5rem;
  margin: 0;
}
</style>
