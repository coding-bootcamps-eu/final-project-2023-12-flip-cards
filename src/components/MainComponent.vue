<template>
  <div>
    <!-- Single root element -->
    <CardComponent v-if="showCard" msg="Card component" />
  </div>
</template>

<script>
import CardComponent from '@/components/CardComponent.vue'
export default {
  components: {
    CardComponent
  },

  props: {
    showCard: {
      // Add a prop to control the visibility of the CardComponent
      type: Boolean,
      default: true // Default to true, so the card shows unless told otherwise
    }
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
