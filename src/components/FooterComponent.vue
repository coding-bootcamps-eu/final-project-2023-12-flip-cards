<template>
  <div class="footer" id="footer">
    <h1>{{ msg }}</h1>
    <h2>"Part Time Class 12/2023</h2>
  </div>
</template>

<script>
export default {
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
div {
  border-radius: 25px;
  background-color: var(--vibrant-purple);
  color: white;
  text-align: center;
}

h3 {
  margin: 40px 0 0;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: hsl(310, 47%, 49%);
}
</style>
