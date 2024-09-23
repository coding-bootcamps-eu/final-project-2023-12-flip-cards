<template>
  <header class="app-header">
    <!-- Header as Root Element -->
    <LogoComponent :msg="title" />
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/card">Card</router-link>
      <router-link to="/input">Input</router-link>
      <router-link to="/options">Options</router-link>
      <router-link to="/statistics">Statistics</router-link>
      <router-link to="/about">About</router-link>
      <!-- Added About link -->
    </nav>
  </header>
</template>

<script>
import LogoComponent from '@/components/LogoComponent.vue'

export default {
  name: 'HeaderComponent',
  props: {
    title: {
      type: String,
      default: 'App Title'
    }
  },
  components: {
    LogoComponent
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

.logo-container {
  display: flex;
  flex-direction: column; /* Anordnung von Logo und Titel in einer Spalte */
}

nav {
  display: flex;
  gap: 1rem;
}

nav router-link {
  color: var(--vibrant-purple);
  text-decoration: none;
}

nav router-link:hover {
  text-decoration: underline;
}
</style>
