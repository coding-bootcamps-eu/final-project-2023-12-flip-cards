<template>
  <div>
    <!-- Single root element -->
    <h1>This is the main component</h1>
    <CardComponent msg="Card component" />
  </div>
</template>

<script>
import CardComponent from '@/components/CardComponent.vue'
export default {
  components: {
    CardComponent
  },
  data() {
    return {}
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
* {
  box-sizing: border-box;
}
</style>
