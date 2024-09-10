<template>
  <Logo msg="Logo component" />
  <Card msg="Card component" />
</template>

<script>
import Logo from "../components/LogoComponent.vue";
import Card from "../components/CardComponent.vue";
export default {
  components: {
    Logo,
    Card,
  },
  data() {
    return {
      newCard: "",
      cards: [],
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  box-sizing: border-box;
}
</style>
