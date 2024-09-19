<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Practice Your Cards" msg="One card at a time" />
    </header>

    <!-- Navigation Buttons -->
    <div class="nav-buttons">
      <button @click="prevCard" :disabled="currentIndex === 0">Previous</button>
      <button @click="nextCard" :disabled="currentIndex === filteredCards.length - 1">Next</button>
    </div>

    <!-- Card Display Section -->
    <main>
      <div v-if="filteredCards.length === 0">No cards available. Please create a new card!</div>

      <div v-if="filteredCards.length > 0" class="card-container">
        <div class="card" :class="{ flipped: isFlipped }" @click="flipCard">
          <!-- Front Side of the Card -->
          <div class="card-face card-front">
            <div class="card-header">
              <p>
                Module: {{ currentCard.moduleId }}, Tool: {{ currentCard.toolId }}, Topic:
                {{ currentCard.topicId }}
              </p>
              <h3>{{ currentCard.title }}</h3>
            </div>
            <div class="card-body">
              <p><strong>Side 1:</strong> {{ currentCard.text_1 }}</p>
            </div>
          </div>

          <!-- Back Side of the Card -->
          <div class="card-face card-back">
            <div class="card-body">
              <p><strong>Side 2:</strong> {{ currentCard.text_2 }}</p>
            </div>
            <div class="card-footer">
              <p><strong>Status:</strong> {{ currentCard.status }}</p>
              <p><strong>Times Practiced:</strong> {{ currentCard.times_practiced }}</p>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Filter Buttons -->
    <div class="filter-buttons">
      <button @click="filterCards('all')">All</button>
      <button @click="filterCards('new')">New</button>
      <button @click="filterCards('review needed')">Review Needed</button>
      <button @click="filterCards('confident')">Confident</button>
    </div>

    <!-- Footer -->
    <footer>
      <FooterComponent msg="Footer component" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'CardView',
  components: {
    HeaderComponent,
    FooterComponent
  },
  data() {
    return {
      cards: [], // Store all fetched cards
      filteredCards: [], // Store the filtered cards based on status
      currentIndex: 0, // Index of the current card being displayed
      isFlipped: false, // Track if the card is flipped
      selectedFilter: 'all' // Store the current filter (new, review needed, etc.)
    }
  },
  computed: {
    currentCard() {
      // Get the card at the current index of the filtered cards
      return this.filteredCards[this.currentIndex]
    }
  },
  methods: {
    fetchCards() {
      // Fetch all the cards from the API
      fetch('http://localhost:3001/cards')
        .then((response) => response.json())
        .then((data) => {
          this.cards = data // Assign the fetched cards to the 'cards' array
          this.filterCards('all') // Initially display all cards
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    filterCards(status) {
      // Filter cards based on the selected status
      if (status === 'all') {
        this.filteredCards = this.cards // Show all cards
      } else {
        this.filteredCards = this.cards.filter((card) => card.status === status)
      }
      this.currentIndex = 0 // Reset the current card index when the filter changes
      this.isFlipped = false // Reset the flip state
      this.selectedFilter = status // Update the selected filter
    },
    nextCard() {
      // Move to the next card if possible
      if (this.currentIndex < this.filteredCards.length - 1) {
        this.currentIndex++
        this.isFlipped = false // Reset the flip state when changing cards
      }
    },
    prevCard() {
      // Move to the previous card if possible
      if (this.currentIndex > 0) {
        this.currentIndex--
        this.isFlipped = false // Reset the flip state when changing cards
      }
    },
    flipCard() {
      // Toggle the flip state
      this.isFlipped = !this.isFlipped
    }
  },
  mounted() {
    // Call the fetchCards method when the component is mounted
    this.fetchCards()
  }
}
</script>

<style scoped>
.card-container {
  perspective: 1000px;
  width: 100%;
  max-width: 400px;
  margin: 0 auto;
}

.card {
  width: 100%;
  height: 250px;
  transition: transform 0.6s;
  transform-style: preserve-3d;
  cursor: pointer;
  position: relative;
}

.card.flipped {
  transform: rotateY(180deg);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  border: 1px solid #ccc;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.card-front {
  background-color: #f9f9f9;
}

.card-back {
  background-color: #fff;
  transform: rotateY(180deg);
}

.card-header h3 {
  margin: 0;
}

.card-body {
  margin-top: 10px;
}

.card-footer {
  margin-top: 20px;
  font-size: 0.9em;
  color: #666;
}

.nav-buttons {
  display: flex;
  justify-content: space-between;
  margin: 20px auto;
  max-width: 400px;
}

button {
  padding: 10px 20px;
  background-color: #6200ea;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
}

button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}

.filter-buttons {
  display: flex;
  justify-content: space-evenly;
  margin: 20px 0;
}

.filter-buttons button {
  padding: 10px 15px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.filter-buttons button:hover {
  background-color: #0056b3;
}

.filter-buttons button:disabled {
  background-color: #ccc;
  cursor: not-allowed;
}
</style>
