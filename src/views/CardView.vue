<template>
  <div>
    <header class="app-header">
      <h1>Card View</h1>

      <nav>
        <router-link to="/">Home</router-link>
        <router-link to="/card">Card</router-link>
        <router-link to="/about">About</router-link>
        <router-link to="/input">Input</router-link>
        <router-link to="/options">Options</router-link>
        <router-link to="/statistics">Statistics</router-link>
      </nav>
    </header>
    <div>
      <h1>This is the main component, and the card needs to be flipped yet</h1>

      <!-- Flip Card View -->
      <div class="flip-card">
        <div class="card-header">
          <!-- Display current module, tool, and topic -->
          <p>Modul: {{ currentModule }}</p>
          <p>Tool: {{ currentTool }}</p>
          <p>Topic: {{ currentTopic }}</p>
        </div>

        <!-- Card Content -->
        <div class="card-content">
          <h2>{{ cardTitle }}</h2>
          <p>{{ cardText }}</p>
          <p>{{ timesPracticed }} times practiced</p>
        </div>

        <!-- Buttons for moving card to different stacks -->
        <div class="card-actions">
          <button @click="moveToNewStack">Move to stack new</button>
          <button @click="moveToReviewStack">Move to stack review</button>
          <button @click="moveToConfidentStack">Move to stack confident</button>
          <button @click="moveToArchivedStack">Move to stack archived</button>
        </div>
      </div>
    </div>
    <MainComponent />
    <FooterComponent />
  </div>
</template>

<script>
import MainComponent from '@/components/MainComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'CardView',
  components: {
    MainComponent,
    FooterComponent
  },
  data() {
    return {
      module: 'Modul X',
      tool: 'Tool X',
      topic: 'Topic X'
    }
  }
}
</script>

<style scoped>
.app-header {
  padding: 10px;
  background-color: var(--vibrant-purple);
  color: var(--white);
  border-bottom: 2px solid var(--light-gray);
  text-align: center;
  font-family: inherit;
}

.module-tool-topic {
  font-size: 1rem;
  margin-bottom: 10px;
}

h1 {
  margin: 0;
  font-size: 1.5rem;
}

nav a {
  margin: 0 10px;
  color: var(--white);
  text-decoration: none;
}

nav a:hover {
  text-decoration: underline;
}
</style>
