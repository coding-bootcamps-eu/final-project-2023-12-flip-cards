<template>
  <div class="wrapper">
    <header>
      <HeaderComponent title="Home Page" msg="Header component" />
    </header>

    <article class="main-layout">
      <!-- App Name Section, centered vertically -->
      <section class="app-name">
        <h1>Flip Card App</h1>
        <p>Your go-to flashcard app</p>
        <p>
          Master your skills with customizable flashcards. Practice anytime, track your progress,
          and improve!
        </p>
        <img :src="imageSrc" alt="A beautiful tent" />
      </section>
    </article>

    <!-- Footer -->
    <footer>
      <FooterComponent msg="Coding Bootcamps Europe" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'
import tentImage from '@/assets/tent.jpg' // Import the image

export default {
  name: 'Home',
  components: {
    HeaderComponent,
    FooterComponent
  },
  data() {
    return {
      imageSrc: tentImage // Store the image path in a data property
    }
  }
}
</script>

<style scoped>
/* This wrapper takes up the full height of the viewport */
.wrapper {
  display: flex;
  flex-direction: column; /* Arrange children in a column */
  height: 100vh; /* Full height of the viewport */
}

/* Main layout takes up most of the viewport height */
.main-layout {
  display: flex;
  flex-direction: column; /* Stack children vertically */
  align-items: center; /* Center children horizontally */
  justify-content: flex-start; /* Start from the top */
  flex: 1; /* This section will grow to fill available space */
}

/* App Name section styling */
.app-name {
  text-align: center;
  margin-top: 2rem;
}

.app-name h1 {
  font-size: 2.5rem;
  margin-bottom: 0.25rem;
}

.app-name p {
  font-size: 1.25rem;
  color: #555;
}

/* Styling for the image */
.app-name img {
  max-width: 100%;
  height: auto;
  margin-top: 1rem;
}
</style>
