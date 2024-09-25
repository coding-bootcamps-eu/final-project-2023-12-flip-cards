<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Practice Your Cards" msg="One card at a time" />
    </header>

    <!-- Tab Container -->
    <div class="tab-container">
      <div class="tabs">
        <span class="tab" :class="{ active: currentTab === 0 }">
          Module:
          <select v-model="selectedModule" @change="updateAvailableTools">
            <option value="">Alle</option>
            <option v-for="module in modules" :key="module.id" :value="module.id">
              {{ module.name }}
            </option>
          </select>
        </span>

        <span class="tab" :class="{ active: currentTab === 1 }">
          Tool:
          <select v-model="selectedTool" @change="updateAvailableTopics">
            <option value="">Alle</option>
            <option v-for="tool in availableTools" :key="tool.id" :value="tool.id">
              {{ tool.name }}
            </option>
          </select>
        </span>

        <span class="tab" :class="{ active: currentTab === 2 }">
          Topic:
          <select v-model="selectedTopic">
            <option value="">Alle</option>
            <option v-for="topic in availableTopics" :key="topic.id" :value="topic.id">
              {{ topic.name }}
            </option>
          </select>
        </span>
      </div>
    </div>

    <!-- Karte anzeigen -->
    <div v-if="currentCard" class="card" :class="{ flipped: isFlipped }" @click="flipCard">
      <div class="card-face card-front">
        <div class="card-header">
          <h3>{{ currentCard.title }}</h3>
        </div>
        <div class="card-body">
          <p><strong>Side 1:</strong> {{ currentCard.text_1 }}</p>
        </div>
      </div>

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

    <!-- Filter Dropdown -->
    <div class="filter-section">
      <label for="filter">Filter Cards:</label>
      <select id="filter" v-model="selectedFilter" @change="applyFilter">
        <option value="all">All</option>
        <option value="new">New</option>
        <option value="review needed">Review Needed</option>
        <option value="confident">Confident</option>
        <option value="archived">Archived</option>
      </select>
    </div>

    <!-- Status Update Radio Buttons -->
    <div class="status-update">
      <h4>Update Card Status:</h4>
      <label>
        <input type="radio" v-model="newStatus" value="review needed" @change="updateCardStatus" />
        Review Needed
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
      <FooterComponent msg="Footer component" />
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
    async fetchCards() {
      try {
        const response = await fetch('http://localhost:3001/cards') // URL zu deiner API
        const data = await response.json()
        this.cards = data // Store fetched cards
        this.filteredCards = this.cards // Set the filtered cards
        this.updateCounts() // Update the counts after fetching cards
      } catch (error) {
        console.error('Error fetching cards:', error)
      }
    },
    async fetchCategories() {
      // API-Anfragen, um Module, Tools und Themen zu laden
      const modulesResponse = await fetch('http://localhost:3001/modules')
      const toolsResponse = await fetch('http://localhost:3001/tools')
      const topicsResponse = await fetch('http://localhost:3001/topics')

      this.modules = await modulesResponse.json()
      this.tools = await toolsResponse.json()
      this.topics = await topicsResponse.json()
    },
    setActiveTab(index) {
      this.currentTab = index
    },
    updateAvailableTools() {
      // Filter available tools based on selected module
      if (this.selectedModule) {
        this.availableTools = this.tools.filter((tool) => tool.moduleId === this.selectedModule)
      } else {
        this.availableTools = this.tools // Show all tools if no module is selected
      }
      this.selectedTool = null // Reset selected tool
      this.updateAvailableTopics() // Update available topics
    },
    updateAvailableTopics() {
      // Filter available topics based on selected tool
      if (this.selectedTool) {
        this.availableTopics = this.topics.filter((topic) => topic.toolId === this.selectedTool)
      } else {
        this.availableTopics = this.topics // Show all topics if no tool is selected
      }
    },
    updateCurrentCard() {
      this.currentIndex = 0 // Reset the index

      // Filter cards based on selected IDs
      this.filteredCards = this.cards.filter(
        (card) =>
          (!this.selectedModule || card.moduleId === this.selectedModule) &&
          (!this.selectedTool || card.toolId === this.selectedTool) &&
          (!this.selectedTopic || card.topicId === this.selectedTopic)
      )
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
    flipCard() {
      this.isFlipped = !this.isFlipped // Flip the card
    },
    updateCardStatus() {
      if (this.currentCard) {
        this.currentCard.status = this.newStatus // Update the status of the current card
        // Here you can also add an API request to update the status in the database
        console.log(`Kartenstatus für ${this.currentCard.title} aktualisiert auf ${this.newStatus}`)
      }
    }
  },
  mounted() {
    this.fetchCards() // Load the cards when the component is mounted
    this.fetchCategories() // Load modules, tools, and topics
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
  position: relative; /* Positionierung für die Tabs */
  z-index: 1; /* Höherer z-index für die Tabs */
}

.tabs {
  display: flex; /* Flexbox für die Registerkarten */
  margin-bottom: -20px; /* Negative Margin, um die Karte zu überlappen */
}

.tab {
  display: inline-block;
  padding: 0.7em 2em; /* Innenabstand */
  color: var(--white); /* Schriftfarbe */
  background-color: var(--vibrant-purple); /* Hintergrundfarbe */
  border: 1px solid white;
  border-radius: 30px 30px 0 0; /* Nur oben gerundet */
  margin-right: -5px; /* Überlappung der Registerkarten */
  cursor: pointer; /* Zeiger-Cursor */
  transition: background-color 0.3s; /* Übergangseffekte */
  width: 180px;
}

/* Dropdown-Stil */
.tab select {
  margin-left: 0px; /* Abstand zwischen dem Tab-Text und dem Dropdown */
  background-color: var(--vibrant-purple); /* Hintergrundfarbe des Dropdowns */
  color: var(--white); /* Schriftfarbe des Dropdowns */
  padding: 0em; /* Innenabstand für das Dropdown */
  border-radius: 0px; /* Gerundete Ecken für das Dropdown */
  border: 0px solid #ccc; /* Rahmen für das Dropdown */
  cursor: pointer; /* Zeiger-Cursor */
  width: 150px; /* Feste Breite für das Dropdown */
}

/* Karte */
.card {
  width: 530px; /* Breite der Karte */
  height: 300px; /* Höhe der Karte */
  perspective: 1000px; /* Perspektive für 3D-Effekt */
  cursor: pointer; /* Zeiger-Cursor */
  position: relative; /* Positionierung für die Karte */
  margin-top: -30px; /* Negative Margin, um die Karte nach oben zu verschieben */
  z-index: 0; /* Niedrigerer z-index, damit die Karte hinter den Tabs liegt */
  box-shadow: 10px 10px 10px 10px rgba(0, 0, 0, 0.4);
}
.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  transition: transform 0.6s;
  border: 2px solid var(--vibrant-purple);
}

.card-front {
  background-color: var(--thistle);
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
  margin-top: 2rem;
  margin-left: 1rem;
}

.dropdown-container {
  margin: 2px 0; /* Abstand für die Dropdowns */
}
/* Status Update Radio Buttons */
.status-update {
  display: flex;
  justify-content: center;
  margin-top: 1rem;
}

.status-update label {
  margin-inline: 10px;
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
