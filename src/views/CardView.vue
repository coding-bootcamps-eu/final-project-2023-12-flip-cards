<template>
  <header><HeaderComponent title="Home Page" msg="Header component" /></header>
  <main><MainComponent msg="Main component" /></main>
  <footer><FooterComponent msg="Footer component" /></footer>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import MainComponent from '@/components/MainComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'App',
  components: {
    MainComponent,
    FooterComponent
  }
}
</script>

<style scoped>
.app-header {
  padding: 10px;
  background-color: var(--vibrant-purple);
  color: var(--white);
  border-bottom: 2px solid var(--light-gray);
  text-align: center;
  font-family: inherit;
}

.module-tool-topic {
  font-size: 1rem;
  margin-bottom: 10px;
}

h1 {
  margin: 0;
  font-size: 1.5rem;
}

nav a {
  margin: 0 10px;
  color: var(--white);
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
}
</style>
