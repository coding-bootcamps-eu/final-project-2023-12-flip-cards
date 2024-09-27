<template>
  <div class="app-container">
    <header class="header">
      <HeaderComponent title="Home Page" msg="Header component" />
    </header>

    <article class="main-layout">
      <h1>Flip Card App</h1>
      <p>Your go-to flashcard app</p>
      <p>
        Master your skills with customizable flashcards. Practice anytime, track your progress, and
        improve!
      </p>
      <img :src="imageSrc" alt="A beautiful tent" />
    </article>

    <footer class="footer">
      <FooterComponent msg="Coding Bootcamps Europe" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'
import tentImage from '@/assets/tent.jpg' // Import the image

export default {
  name: 'HomeComponent',
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
/* Full viewport height for the app container */
.app-container {
  display: flex;
  flex-direction: column;
  height: 100vh; /* Full height of the viewport */
}

/* Fixed header at the top */
.header {
  flex: 0 0 auto; /* Fixed size, do not grow or shrink */
}

/* Main layout takes up the remaining space */
.main-layout {
  flex: 1; /* This section will grow to fill available space */
  display: flex;
  flex-direction: column;
  align-items: center; /* Center children horizontally */
  justify-content: center; /* Center children vertically */
}

/* Fixed footer at the bottom */
.footer {
  flex: 0 0 auto; /* Fixed size, do not grow or shrink */
}

/* Styling for the image */
.main-layout img {
  max-width: 100%;
  height: auto;
  margin-top: 1rem;
}
</style>
