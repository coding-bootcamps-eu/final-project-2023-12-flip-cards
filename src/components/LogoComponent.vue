<template>
  <h1>{{ msg }}</h1>
  <body>
    <div class="triangle-container">
      <div class="rounded-wurst top"></div>
      <div class="rounded-wurst left"></div>
      <div class="rounded-wurst right"></div>
      <div class="small-triangle"></div>
      <!-- Kleines Dreieck -->
    </div>
  </body>
</template>

<script>
export default {
  props: {
    msg: {
      type: String,
      required: true
    }
  }
}
</script>

<style scoped>
body {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: #f0f0f0;
}

.triangle-container {
  position: relative;
  width: 120px;
  height: 120px;
  background-color: transparent;
  border-radius: 50%;
  transform-origin: center;
  transition: transform 0.5s ease;
}

.rounded-wurst {
  position: absolute;
  background-color: #6a1cc3;
  border-radius: 25px;
}

.top {
  width: 100px;
  height: 30px;
  top: 65.8px;
  left: 10.5px;
}

.left {
  width: 30px;
  height: 100px;
  top: 0px;
  left: 27px;
  transform: rotate(30deg);
}

.right {
  width: 30px;
  height: 100px;
  top: 0px;
  right: 27px;
  transform: rotate(-30deg);
}

.small-triangle {
  position: absolute;
  width: 0;
  height: 0;
  border-left: 15px solid transparent;
  border-right: 15px solid transparent;
  border-bottom: 25px solid white;
  top: 43px;
  left: 50%;
  transform: translateX(-50%);
}

/* Hover-Effekt für das Dreieck */
.triangle-container:hover {
  transform: rotate(-30deg);
}
</style>
