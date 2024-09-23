<template>
  <div class="about-page">
    <header>
      <HeaderComponent title="About This App" msg="Learn more about our app and its features." />
    </header>

    <main>
      <div class="about-content">
        <section>
          <h2>Overview</h2>
          <p>
            Welcome to our flashcard app! The app is designed to enhance your learning by providing
            an interactive and engaging way to practice key concepts. Flip through flashcards,
            review their contents, and mark your confidence level for better retention.
          </p>
        </section>

        <section>
          <h2>How It Works</h2>
          <p>
            Navigate through flashcards using the arrows. Flip a card to reveal additional
            information, and use the status options to track your understanding. You can filter
            cards based on your confidence level, ensuring you focus on areas that need more review.
          </p>
        </section>

        <section>
          <h2>Features</h2>
          <ul>
            <li>Interactive flashcard flipping mechanism</li>
            <li>Mark cards as "Review Needed" or "Confident"</li>
            <li>Filter flashcards by status to focus on what matters</li>
            <li>Responsive design for use on mobile, tablet, or desktop</li>
          </ul>
        </section>

        <section>
          <h2>Why We Created This App</h2>
          <p>
            We believe that active recall and spaced repetition are key to mastering new knowledge.
            This app provides a simple, effective way to implement those learning techniques,
            helping users retain information better.
          </p>
        </section>

        <section>
          <h2>Contact Us</h2>
          <p>
            Have feedback or need assistance? Feel free to reach out at
            <a href="mailto:support@example.com">someone at the Bootcamp :D</a>.
          </p>
        </section>
      </div>
    </main>

    <footer>
      <FooterComponent msg="Cooding Bootcamps Europe" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'AboutView',
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

.about-content {
  flex-grow: 1;
  padding: 2rem;
  max-width: 800px;
  margin: 0 auto;
  line-height: 1.6;
}

.about-content h2 {
  color: var(--vibrant-purple);
  margin-bottom: 1rem;
}

.about-content p {
  margin-bottom: 1rem;
}

.about-content ul {
  list-style-type: disc;
  padding-left: 1.5rem;
}

.about-content a {
  color: var(--vibrant-purple);
  text-decoration: underline;
}

.about-content a:hover {
  text-decoration: none;
}

footer {
  margin-top: auto;
  padding: 1rem;
  background-color: var(--footer-background);
  text-align: center;
}
</style>
