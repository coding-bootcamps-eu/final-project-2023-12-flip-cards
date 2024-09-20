<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Practice Your Cards" msg="One card at a time" />
    </header>

    <!-- Filter Dropdown -->
    <div class="filter-section">
      <label for="filter">Filter Cards:</label>
      <select id="filter" v-model="selectedFilter" @change="applyFilter">
        <option value="all">All</option>
        <option value="review needed">Review Needed</option>
        <option value="confident">Confident</option>
      </select>
    </div>

    <!-- Status Update Radio Buttons -->
    <div class="status-update">
      <label>
        <input type="radio" v-model="newStatus" value="review needed" @change="updateCardStatus" />
        Review Needed
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="confident" @change="updateCardStatus" />
        Confident
      </label>
    </div>

    <!-- Navigation Arrows -->
    <div class="card-container">
      <button class="nav-arrow left-arrow" @click="prevCard" :disabled="currentIndex === 0">
        &#9664;
        <!-- Left Arrow Symbol -->
      </button>

      <div v-if="filteredCards.length === 0">No cards available. Please create a new card!</div>

      <div
        v-if="filteredCards.length > 0"
        class="card"
        :class="{ flipped: isFlipped }"
        @click="flipCard"
      >
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

      <button
        class="nav-arrow right-arrow"
        @click="nextCard"
        :disabled="currentIndex === filteredCards.length - 1"
      >
        &#9654;
        <!-- Right Arrow Symbol -->
      </button>
    </div>

    <!-- Footer -->
    <footer>
      <FooterComponent msg="Footer content" />
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
      cards: [], // Cards fetched from the API
      currentIndex: 0,
      isFlipped: false,
      selectedFilter: 'all',
      newStatus: '',
      filteredCards: [] // Cards after applying the filter
    }
  },
  computed: {
    currentCard() {
      return this.filteredCards[this.currentIndex]
    }
  },
  methods: {
    fetchCards() {
      fetch('http://localhost:3001/cards')
        .then((response) => response.json())
        .then((data) => {
          this.cards = data
          this.applyFilter()
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    applyFilter() {
      if (this.selectedFilter === 'all') {
        this.filteredCards = this.cards
      } else {
        this.filteredCards = this.cards.filter((card) => card.status === this.selectedFilter)
      }
      this.currentIndex = 0 // Reset to the first card after applying filter
    },
    flipCard() {
      this.isFlipped = !this.isFlipped
    },
    prevCard() {
      if (this.currentIndex > 0) {
        this.currentIndex--
        this.isFlipped = false // Reset flip when moving to previous card
      }
    },
    nextCard() {
      if (this.currentIndex < this.filteredCards.length - 1) {
        this.currentIndex++
        this.isFlipped = false // Reset flip when moving to next card
      }
    },
    updateCardStatus() {
      const updatedCard = { ...this.currentCard, status: this.newStatus }

      fetch(`http://localhost:3001/cards/${this.currentCard.id}`, {
        method: 'PATCH',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({ status: this.newStatus })
      })
        .then((response) => {
          if (response.ok) {
            this.$set(this.cards, this.currentIndex, updatedCard)
            this.applyFilter() // Reapply filter to reflect changes
          } else {
            console.error('Error updating status')
          }
        })
        .catch((error) => {
          console.error('Error updating status:', error)
        })
    }
  },
  created() {
    this.fetchCards()
  }
}
</script>

<style scoped>
.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px;
  position: relative; /* To allow positioning of arrows */
}

.card {
  width: 300px;
  height: 200px;
  perspective: 1000px;
  cursor: pointer;
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  transition: transform 0.6s;
}

.card-front {
  background-color: var(--vibrant-purple);
  color: var(--white);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 1rem;
}

.card-back {
  background-color: var(--peach-light-orange);
  color: var(--white);
  transform: rotateY(180deg);
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  padding: 1rem;
}

.card.flipped .card-front {
  transform: rotateY(180deg);
}

.card.flipped .card-back {
  transform: rotateY(0deg);
}

/* Navigation Arrows */
.nav-arrow {
  background: none;
  border: none;
  font-size: 2rem;
  cursor: pointer;
  color: var(--vibrant-purple);
  padding: 0 20px;
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  z-index: 10;
}

.left-arrow {
  left: 0;
}

.right-arrow {
  right: 0;
}

.nav-arrow:disabled {
  color: #ccc;
  cursor: not-allowed;
}

.filter-section {
  margin-bottom: 1rem;
}

/* Status Update Radio Buttons */
.status-update {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

.status-update label {
  margin-right: 10px;
}

.status-update input[type='radio'] {
  margin-right: 5px;
}

/* Responsive Design for Mobile Landscape View  */
@media (max-width: 600px) and (orientation: landscape) {
  .card-container {
    height: 100vh;
    justify-content: center;
    align-items: center;
  }

  .card {
    width: 90vw;
    height: 60vh;
  }

  .nav-arrow {
    font-size: 1.5rem;
  }

  .nav-buttons {
    display: none;
  }

  .status-update {
    display: none;
  }
}
</style>
