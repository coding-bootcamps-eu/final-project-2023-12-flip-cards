<template>
  <div id="app">
    <!-- Container für die gesamte App -->
    <LogoComponent />
    <main>
      <router-view />
      <!-- Hier wird der Inhalt der Route angezeigt -->
    </main>
  </div>
</template>

<script>
import LogoComponent from "./components/LogoComponent.vue";
export default {
  components: {
    LogoComponent,
  },
};
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
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
