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

      <div class="tab-container">
        <div class="tabs">
          <span class="tab" :class="{ active: currentTab === 0 }">
            Module:
            <select v-model="selectedModule" @change="updateCurrentCard" style="margin-left: 5px">
              <option v-for="module in modules" :key="module.id" :value="module.id">
                {{ module.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 1 }">
            Tool:
            <select v-model="selectedTool" @change="updateCurrentCard" style="margin-left: 5px">
              <option v-for="tool in tools" :key="tool.id" :value="tool.id">
                {{ tool.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 2 }">
            Topic:
            <select v-model="selectedTopic" @change="updateCurrentCard" style="margin-left: 5px">
              <option v-for="topic in topics" :key="topic.id" :value="topic.id">
                {{ topic.name }}
              </option>
            </select>
          </span>

          <span class="tab" :class="{ active: currentTab === 3 }">
            Current Stack:
            <select v-model="selectedFilter" @change="applyFilter" style="margin-left: 5px">
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
      </button>
    </div>

    <!-- Status Update Radio Buttons -->
    <div class="status-update">
      <h4>Move to stack</h4>
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
      modules: [], // Array für Module
      tools: [], // Array für Tools
      topics: [] // Array für Themen
    }
  },
  computed: {
    currentCard() {
      return this.filteredCards[this.currentIndex] || null // Gibt die aktuelle Karte zurück oder null
    }
  },
  methods: {
    async fetchCards() {
      try {
        const response = await fetch('http://localhost:3001/cards') // URL zu deiner API
        const data = await response.json()
        this.cards = data // Setze alle Karten

        // Hole die Details für jedes Modul, Tool und Thema
        for (let card of this.cards) {
          const [moduleResponse, toolResponse, topicResponse] = await Promise.all([
            fetch(`http://localhost:3001/modules/${card.moduleId}`),
            fetch(`http://localhost:3001/tools/${card.toolId}`),
            fetch(`http://localhost:3001/topics/${card.topicId}`)
          ])

          const moduleData = await moduleResponse.json()
          const toolData = await toolResponse.json()
          const topicData = await topicResponse.json()

          // Füge die Module, Tools und Themen zu jeder Karte hinzu
          card.module = moduleData
          card.tool = toolData
          card.topic = topicData
        }

        this.applyFilter() // Wende den Filter an, um die Karten beim ersten Laden anzuzeigen
      } catch (error) {
        console.error('Fehler beim Abrufen der Karten:', error)
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
    applyFilter() {
      if (this.selectedFilter === 'all') {
        this.filteredCards = this.cards // Alle Karten anzeigen
      } else {
        this.filteredCards = this.cards.filter((card) => card.status === this.selectedFilter) // Gefilterte Karten
      }
      this.currentIndex = 0 // Setze den Index zurück
      this.isFlipped = false // Setze den Zustand für das Umdrehen der Karte zurück
    },
    updateCurrentCard() {
      this.currentIndex = 0 // Setze den Index zurück
      // Filtere die Karten basierend auf den ausgewählten IDs
      this.filteredCards = this.cards.filter(
        (card) =>
          card.moduleId === this.selectedModule &&
          card.toolId === this.selectedTool &&
          card.topicId === this.selectedTopic
      )
    },
    flipCard() {
      this.isFlipped = !this.isFlipped // Umdrehen der Karte
    },
    updateCardStatus() {
      if (this.currentCard) {
        this.currentCard.status = this.newStatus // Aktualisiere den Status der aktuellen Karte
        // Hier kannst du auch eine API-Anfrage hinzufügen, um den Status in der Datenbank zu aktualisieren
        console.log(`Kartenstatus für ${this.currentCard.title} aktualisiert auf ${this.newStatus}`)
      }
    }
  },
  mounted() {
    this.fetchCards() // Lade die Karten
    this.fetchCategories() // Lade die Module, Tools und Themen
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

.tab-container {
  display: flex;
  justify-content: center; /* Zentriert die Registerkarten */
  margin-bottom: 1rem; /* Abstand nach unten */
}

.tabs {
  display: flex; /* Flexbox für die Registerkarten */
}

.tab {
  display: inline-block; /* Inline-Block für die Registerkarten */
  padding: 10px 20px; /* Innenabstand */
  background-color: #32557f; /* Hintergrundfarbe */
  color: #fff; /* Schriftfarbe */
  border-radius: 30px; /* Gerundete Ecken */
  margin: 0 5px; /* Abstand zwischen den Registerkarten */
  cursor: pointer; /* Zeiger-Cursor */
  transition:
    background-color 0.3s,
    color 0.3s; /* Übergangseffekte */
}

/* Hover-Effekt */
.tab:hover {
  background-color: #7eb4e2; /* Hintergrundfarbe beim Hover */
}

/* Aktive Registerkarte */
.tab.active {
  background-color: #fff; /* Hintergrundfarbe für die aktive Registerkarte */
  color: #000; /* Schriftfarbe für die aktive Registerkarte */
}

.tab select {
  margin-left: 5px; /* Abstand zwischen dem Tab-Text und dem Dropdown-Menü */
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
