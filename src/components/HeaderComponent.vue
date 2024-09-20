<template>
  <header class="app-header">
    <!-- Header as Root Element -->
    <LogoComponent :msg="title" />
    <h1>{{ title }}</h1>
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
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 1rem;
  background-color: var(--header-background);
}

nav {
  display: flex;
  gap: 1rem;
}

h1 {
  margin-left: 1rem;
  color: var(--vibrant-purple);
}

nav a {
  color: var(--vibrant-purple);
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
}
</style>
