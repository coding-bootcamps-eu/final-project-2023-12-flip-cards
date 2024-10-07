<template>
  <div id="app">
    <!-- Container für die gesamte App -->

    <router-view />
    <!-- Hier wird der Inhalt der Route angezeigt -->
  </div>
</template>

<script>
export default {
  data() {
    return {
      apiUrl: import.meta.env.VITE_API_URL
    }
  }
}
</script>

<style>
* {
  box-sizing: border-box;
  padding: 0;
}

header {
  line-height: 1.5;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: center;
    flex-wrap: wrap;
  }
}
</style>
