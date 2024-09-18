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

    <div class="flip-card-container">
      <!-- Flip Card View -->
      <div class="flip-card" :class="{ flipped: isFlipped }" @click="flipCard">
        <!-- Card Front Side -->
        <div class="flip-card-front">
          <div class="card-header">
            <p>Modul: {{ currentModule }}</p>
            <p>Tool: {{ currentTool }}</p>
            <p>Topic: {{ currentTopic }}</p>
            <p>{{ timesPracticed }} times practiced</p>
          </div>
          <div class="card-content">
            <h2>{{ cardTitle }}</h2>
            <p>{{ cardText1 }}</p>
          </div>
        </div>

        <!-- Card Back Side -->
        <div class="flip-card-back">
          <div class="card-header">
            <p>Modul: {{ currentModule }}</p>
            <p>Tool: {{ currentTool }}</p>
            <p>Topic: {{ currentTopic }}</p>
            <p>{{ timesPracticed }} times practiced</p>
          </div>
          <div class="card-content">
            <h2>{{ cardTitle }}</h2>
            <p>{{ cardText2 }}</p>
          </div>
        </div>
      </div>

      <!-- Buttons for moving card to different stacks -->
      <div class="card-actions">
        <button @click="moveToNewStack">Move to stack new</button>
        <button @click="moveToReviewStack">Move to stack review</button>
        <button @click="moveToConfidentStack">Move to stack confident</button>
        <button @click="moveToArchivedStack">Move to stack archived</button>
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
      isFlipped: false,
      cardTitle: 'naslov',
      cardText1: 'prvitext',
      cardText2: 'drugiText',
      currentModule: 'prviModul',
      currentTool: 'prviTool',
      currentTopic: 'prvi Topic',
      timesPracticed: 1
    }
  },
  methods: {
    async fetchCardData() {
      try {
        // Use fetch to get the card data
        const response = await fetch('http://localhost:3001/api/cards/2') // Adjust with your actual API endpoint
        const cardData = await response.json() // Parse the JSON data

        // Update the component's data with the fetched card data
        this.cardTitle = cardData.title
        this.cardText1 = cardData.text_1
        this.cardText2 = cardData.text_2
        this.timesPracticed = cardData.times_practiced

        // Adjust tool, module, and topic dynamically based on API data
        this.currentModule = `Modul ${cardData.moduleId}`
        this.currentTool = `Tool ${cardData.toolId}`
        this.currentTopic = `Topic ${cardData.topicId}`
      } catch (error) {
        console.error('Error fetching card data:', error)
      }
    },
    flipCard() {
      this.isFlipped = !this.isFlipped
    },
    moveToNewStack() {
      // Logic to move card to "new" stack
    },
    moveToReviewStack() {
      // Logic to move card to "review" stack
    },
    moveToConfidentStack() {
      // Logic to move card to "confident" stack
    },
    moveToArchivedStack() {
      // Logic to move card to "archived" stack
    }
  },
  mounted() {
    // Fetch the card data when the component is mounted
    this.fetchCardData()
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

.flip-card-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.flip-card {
  width: 300px;
  height: 400px;
  perspective: 1000px;
  cursor: pointer;
}

.flip-card-front,
.flip-card-back {
  width: 100%;
  height: 100%;
  position: absolute;
  backface-visibility: hidden;
  transition: transform 0.6s ease;
}

.flip-card-front {
  background-color: #fff;
}

.flip-card-back {
  background-color: #f0f0f0;
  transform: rotateY(180deg);
}

.flip-card.flipped .flip-card-front {
  transform: rotateY(180deg);
}

.flip-card.flipped .flip-card-back {
  transform: rotateY(360deg);
}

.card-header,
.card-content {
  padding: 20px;
}

.card-actions {
  margin-top: 20px;
}

.card-actions button {
  margin: 5px;
  padding: 10px;
}
</style>
