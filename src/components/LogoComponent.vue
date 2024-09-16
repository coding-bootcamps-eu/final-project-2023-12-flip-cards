<template>
  <h1>{{ msg }}</h1>
  <body>
    <div class="triangle-container">
      <div class="rounded-wurst top"></div>
      <div class="rounded-wurst left"></div>
      <div class="rounded-wurst right"></div>
      <div class="small-triangle"></div>
      <!-- Small Triangle -->
      <svg class="refresh-icon" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path
          d="M17.65 6.35a7.95 7.95 0 00-11.3 0 7.95 7.95 0 000 11.3 7.95 7.95 0 0011.3 0l1.4 1.4a9.969 9.969 0 01-14.1 0 9.969 9.969 0 010-14.1 9.969 9.969 0 0114.1 0l-1.4 1.4zm-5.3 5.3V5h2v6.65l2.35-2.35 1.4 1.4-5 5-5-5 1.4-1.4 2.35 2.35z"
        />
      </svg>
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

/* Hover effect for the triangle */
.triangle-container:hover {
  transform: rotate(-30deg);
}

/* Style for the refresh icon */
.refresh-icon {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  width: 24px; /* Adjust the size as needed */
  height: 24px;
  fill: #6a1cc3; /* Adjust the color as needed */
  pointer-events: none; /* Makes the icon non-interactive */
}
</style>
