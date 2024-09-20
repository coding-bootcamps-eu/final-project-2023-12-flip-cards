<template>
  <div class="about-page">
    <header>
      <HeaderComponent title="About Us" msg="Learn more about this project" />
    </header>

    <main>
      <section class="about-section">
        <h2>About This Application</h2>
        <p>
          This project is a powerful learning tool designed to help users enhance their skills and
          knowledge through interactive flashcards and detailed statistics.
        </p>
        <p>
          Whether you're reviewing important concepts or challenging yourself with new content, this
          app provides a customizable and efficient way to track your progress and improve over
          time.
        </p>

        <h2>Features</h2>
        <ul>
          <li>Interactive flashcards for various subjects</li>
          <li>Statistics to track progress and areas for improvement</li>
          <li>Customizable study options to meet your learning style</li>
          <li>Responsive design for use on mobile and desktop devices</li>
        </ul>

        <h2>Our Vision</h2>
        <p>
          Our mission is to provide learners with an intuitive and flexible platform to improve
          their knowledge retention, confidence, and academic success.
        </p>
      </section>
    </main>

    <footer>
      <FooterComponent msg="Footer component" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'AboutComponent',
  components: {
    HeaderComponent,
    FooterComponent
  }
}
</script>

<style scoped>
.about-page {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
}

header,
footer {
  background-color: var(--header-background);
}

.about-section {
  flex-grow: 1;
  padding: 2rem;
  max-width: 800px;
  margin: 0 auto;
  text-align: center;
}

h2 {
  color: var(--vibrant-purple);
  margin-bottom: 1rem;
}

p {
  margin-bottom: 1.5rem;
  line-height: 1.6;
}

ul {
  list-style-type: none;
  padding: 0;
}

ul li {
  background-color: var(--peach-light-orange);
  padding: 0.5rem;
  margin: 0.5rem 0;
  border-radius: 8px;
}

ul li::before {
  content: '✔';
  margin-right: 0.5rem;
  color: var(--vibrant-purple);
}
</style>
