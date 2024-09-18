<template>
  <header><HeaderComponent title="Home Page" msg="Header component" /></header>
  <main><MainComponent msg="Main component" /></main>
  <footer><FooterComponent msg="Footer component" /></footer>
</template>

<script>
import HeaderComponent from "@/components/HeaderComponent.vue";
import MainComponent from "@/components/MainComponent.vue";
import FooterComponent from "@/components/FooterComponent.vue";

export default {
  name: "App",
  components: {
    HeaderComponent,
    MainComponent,
    FooterComponent,
  },
};
</script>
