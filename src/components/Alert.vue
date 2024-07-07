<!-- Alert.vue -->
<template>
  <div v-if="message" :class="[alertClass]" class="custom-alert">
    {{ message }}
  </div>
</template>

<script>
export default {
  props: {
    message: String,
    type: {
      type: String,
      default: 'success' // Valor por defecto 'success'
    }
  },
  computed: {
    alertClass() {
      return this.type === 'success' ? 'alert-success' : 'alert-danger';
    }
  }
};
</script>

<style scoped>
.custom-alert {
  position: fixed;
  top: 20px;
  left: 50%;
  transform: translateX(-50%);
  z-index: 1000;
  width: 80%;
  max-width: 600px;
  padding: 1rem;
  border-radius: 0.5rem;
  font-size: 1rem;
  font-weight: bold;
}

.alert-success {
  background-color: #d4edda;
  border-color: #c3e6cb;
  color: #155724;
}

.alert-danger {
  background-color: #f8d7da;
  border-color: #f5c6cb;
  color: #721c24;
}
</style>
