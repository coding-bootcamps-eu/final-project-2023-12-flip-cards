<template>
  <div id="logo" class="logo">
    <!-- Umbenennung auf id=logo um es einfacher adressieren zu können -->
    <h1>{{ msg }}</h1>
    <div class="logo-shape">
      <!-- Umbenennung auf "logo-shape" für mehr Klarheit -->
      <div class="rounded-wurst top"></div>
      <div class="rounded-wurst left"></div>
      <div class="rounded-wurst right"></div>
      <div class="small-triangle"></div>
    </div>
  </div>
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

<style>
#logo {
  display: flex;
  flex-direction: column; /* Vertikale Anordnung von h1 und Logo-Form */
  justify-content: center;
  align-items: center;
  height: 15vh; /* Höhe des Logos */
  background-color: #f0f0f0; /* Hintergrundfarbe für Kontrast */
  color: #6a1cc3;
  margin: 0;
}

.logo-shape {
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

.logo-shape:hover {
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
