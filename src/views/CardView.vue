<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Practice Your Cards" msg="One card at a time" />
    </header>

    <!-- Navigation Arrows -->
    <div class="card-container">
      <button class="nav-arrow left-arrow" @click="prevCard" :disabled="currentIndex === 0">
        &#9664;
      </button>
      <!-- Tab Container -->
      <div class="tab-container">
        <div class="tabs">
          <span class="tab" :class="{ active: currentTab === 0 }" @click="setActiveTab(0)">
            Module:
            <select v-model="selectedModule" @change="updateCurrentCard">
              <option v-for="module in modules" :key="module.id" :value="module.id">
                {{ module.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 1 }" @click="setActiveTab(1)">
            Tool:
            <select v-model="selectedTool" @change="updateCurrentCard">
              <option v-for="tool in tools" :key="tool.id" :value="tool.id">
                {{ tool.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 2 }" @click="setActiveTab(2)">
            Topic:
            <select v-model="selectedTopic" @change="updateCurrentCard">
              <option v-for="topic in topics" :key="topic.id" :value="topic.id">
                {{ topic.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 3 }">
            Current Stack:
            <select v-model="selectedFilter" @change="applyFilter">
              <option value="all">All</option>
              <option value="new">New</option>
              <option value="review needed">Review Needed</option>
              <option value="confident">Confident</option>
              <option value="archived">Archived</option>
            </select>
          </span>
        </div>
      </div>

      <div
        v-if="filteredCards.length > 0"
        class="card"
        :class="{ flipped: isFlipped }"
        @click="flipCard"
      >
        <!-- Front Side of the Card -->
        <div class="card-face card-front">
          <div class="card-header">
            <h3>{{ currentCard.title }}</h3>
          </div>
          <div class="card-body">
            <p><strong>front:</strong> {{ currentCard.text_1 }}</p>
          </div>
        </div>

        <!-- Back Side of the Card -->
        <div class="card-face card-back">
          <div class="card-body">
            <p><strong>back:</strong> {{ currentCard.text_2 }}</p>
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
      </button>
    </div>

    <!-- Status Update Radio Buttons -->
    <div class="status-update">
      <h4>Move to</h4>
      <label>
        <input type="radio" v-model="newStatus" value="review needed" @change="updateCardStatus" />
        Review needed
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="confident" @change="updateCardStatus" />
        Confident
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="new" @change="updateCardStatus" />
        New
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="archived" @change="updateCardStatus" />
        Archived
      </label>
    </div>

    <!-- Footer -->
    <footer>
      <FooterComponent msg="Coding Bootcamps Europe" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'App',
  components: {
    HeaderComponent,
    FooterComponent
  },
  data() {
    return {
      filteredCards: [],
      cards: [],
      isFlipped: false,
      selectedFilter: 'all',
      newStatus: '',
      currentIndex: 0,
      selectedModule: null,
      selectedTool: null,
      selectedTopic: null,
      modules: [], // Array for Modules
      tools: [], // Array for Tools
      topics: [], // Array for Topics
      availableTools: [], // Available Tools based on selected Module
      availableTopics: [], // Available Topics based on selected Tool
      currentTab: 0,
      counts: {
        total: 0,
        today: 0,
        week: 0,
        month: 0,
        reviewNeeded: 0,
        confident: 0
      },
      filterText: ''
    }
  },
  computed: {
    currentCard() {
      return this.filteredCards[this.currentIndex] || null // Returns current card or null
    }
  },
  methods: {
    fetchCards() {
      // Fetch cards from the API
      fetch('http://localhost:3001/cards')
        .then((response) => response.json())
        .then((data) => {
          this.cards = data // Store fetched cards
          this.filteredCards = this.cards // Set the filtered cards
          this.updateCounts() // Update the counts after fetching cards
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    updateCounts() {
      const currentDate = new Date()

      // Get the start of the week and month
      const weekStart = new Date(currentDate)
      weekStart.setDate(currentDate.getDate() - currentDate.getDay()) // Set to start of the week
      const monthStart = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1) // Start of the month

      // Reset counts
      this.counts.total = this.cards.length
      this.counts.today = 0
      this.counts.week = 0
      this.counts.month = 0
      this.counts.reviewNeeded = 0
      this.counts.confident = 0

      // Loop through cards and calculate counts
      this.cards.forEach((card) => {
        const cardDate = new Date(card.date_last_practiced)

        // Count cards practiced today
        if (cardDate.toDateString() === currentDate.toDateString()) {
          this.counts.today++
        }

        // Count cards practiced this week
        if (cardDate >= weekStart) {
          this.counts.week++
        }

        // Count cards practiced this month
        if (cardDate >= monthStart) {
          this.counts.month++
        }

        // Count by card status
        if (card.status === 'review needed') {
          this.counts.reviewNeeded++
        } else if (card.status === 'confident') {
          this.counts.confident++
        }
      })
    },
    filterBy(period) {
      const currentDate = new Date()
      let filterCondition = null

      switch (period) {
        case 'today':
          filterCondition = (cardDate) => cardDate.toDateString() === currentDate.toDateString()
          this.filterText = 'Practiced Today'
          break
        case 'week':
          const weekStart = new Date(currentDate)
          weekStart.setDate(currentDate.getDate() - currentDate.getDay())
          filterCondition = (cardDate) => cardDate >= weekStart
          this.filterText = 'Practiced This Week'
          break
        case 'month':
          const monthStart = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1)
          filterCondition = (cardDate) => cardDate >= monthStart
          this.filterText = 'Practiced This Month'
          break
      }

      if (filterCondition) {
        this.filteredCards = this.cards.filter((card) => {
          const cardDate = new Date(card.date_last_practiced)
          return filterCondition(cardDate)
        })
      }
    },
    filterByStatus(status) {
      this.filteredCards = this.cards.filter((card) => card.status === status)

      if (status === 'review needed') {
        this.filterText = 'Cards Needing Review'
      } else if (status === 'confident') {
        this.filterText = 'Confident Cards'
      }
    }
  },
  mounted() {
    this.fetchCards() // Load cards on component mount
  }
}
</script>

<style scoped>
.card-container {
  display: flex;
  flex-direction: column;
  align-items: center; /* Zentriert den gesamten Inhalt */
}

.dropdown-container {
  margin: 20px 0; /* Abstand oben und unten */
}

.dropdown-container select {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: var(--vibrant-purple);
  color: #fff;
  cursor: pointer;
  width: 150px;
}

.dropdown-container select:hover {
  border-color: var(--vibrant-purple); /* Rahmenfarbe beim Hover */
}

.tab-container {
  display: flex;
  justify-content: center;
  margin-bottom: 1rem;
}

.tabs {
  display: flex;
}

.tab {
  display: inline-block;
  padding: 10px 20px;
  background-color: var(--vibrant-purple);
  color: #fff;
  border-radius: 30px;
  margin: 0 5px;
  cursor: pointer;
  transition:
    background-color 0.3s,
    color 0.3s;
}

.tab:hover {
  background-color: var(--vibrant-purple); /* Eine dunklere Farbe für den Hover-Effekt */
}

.card-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 300px; /* Adjust height as necessary */
  position: relative; /* To allow positioning of the card */
  z-index: 1; /* Ensure card is below tabs */
}

.card {
  width: 300px;
  height: 200px;
  perspective: 1000px;
  cursor: pointer;
  position: relative; /* To allow stacking */
  top: 0; /* Reset position to default */
  z-index: 0; /* Ensure card is below tabs */
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
