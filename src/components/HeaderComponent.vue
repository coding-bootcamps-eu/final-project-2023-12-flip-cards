<template>
  <header class="app-header">
    <h1>{{ title }}</h1>
    <nav>
      <router-link to="/">Home</router-link>
      <router-link to="/card">Card</router-link>
      <router-link to="/about">About</router-link>
      <router-link to="/input">Input</router-link>
      <router-link to="/options">Options</router-link>
      <router-link to="/statistics">Statistics</router-link>
    </nav>
  </header>
</template>

<script>
export default {
  props: {
    title: {
      type: String,
      required: true
    }
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
