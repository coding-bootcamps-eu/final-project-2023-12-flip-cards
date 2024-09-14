<template>
  <LogoComponent />
  <header class="app-header">
    <h1>{{ title }}</h1>
  </header>
</template>

<script>
import LogoComponent from '@/components/LogoComponent.vue'

export default {
  components: {
    LogoComponent
  },
  props: {
    title: {
      type: String,
      required: true,
    },
  },
};
</script>

<style scoped>
.app-header {
  padding: 10px;
  background-color: var(--vibrant-purple);
  color: var(--white);
  border-bottom: 2px solid var(--light-gray);
  text-align: center;
  font-family: inherit; /* Inherit the global font */
}

h1 {
  margin: 0;
  font-size: 1.5rem;
}
</style>
