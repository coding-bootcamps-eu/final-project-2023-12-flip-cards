<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Practice Your Cards" msg="One card at a time" />
    </header>

    <!-- Tab Container -->
    <div class="tab-container">
      <div class="tabs dropdown-container">
        <span class="tab">
          <label for="selectedModule">Module:</label>
          <select id="selectedModule" v-model="selectedModule" @change="updateCurrentCard">
            <option value="">All</option>
            <option v-for="module in modules" :key="module.id" :value="module.id">
              {{ module.name }}
            </option>
          </select>
        </span>

        <span class="tab">
          <label for="selectedTool">Tool:</label>
          <select id="selectedTool" v-model="selectedTool" @change="updateCurrentCard">
            <option value="">All</option>
            <option v-for="tool in tools" :key="tool.id" :value="tool.id">
              {{ tool.name }}
            </option>
          </select>
        </span>

        <span class="tab">
          <label for="selectedTopic">Topic:</label>
          <select id="selectedTopic" v-model="selectedTopic" @change="updateCurrentCard">
            <option value="">All</option>
            <option v-for="topic in topics" :key="topic.id" :value="topic.id">
              {{ topic.name }}
            </option>
          </select>
        </span>
      </div>
    </div>

    <button class="nav-arrow left-arrow" @click="prevCard" :disabled="currentIndex === 0">
      &#9664;
    </button>

    <!-- Karte anzeigen -->
    <div v-if="currentCard" class="card" :class="{ flipped: isFlipped }" @click="flipCard">
      <div class="card-face card-front">
        <h3>{{ currentCard.title }}</h3>
        <p><strong>Side 1:</strong> {{ currentCard.text_1 }}</p>
      </div>
      <div class="card-face card-back">
        <h3>{{ currentCard.title }}</h3>
        <p><strong>Side 2:</strong> {{ currentCard.text_2 }}</p>
        <p><strong>Times Practiced:</strong> {{ currentCard.times_practiced }}</p>
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

    <button
      class="nav-arrow right-arrow"
      @click="nextCard"
      :disabled="currentIndex === filteredCards.length - 1"
    >
      &#9654;
    </button>

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
      cards: [],
      modules: [], // Array für Module
      tools: [], // Array für Tools
      topics: [], // Array für Themen
      selectedModule: null,
      selectedTool: null,
      selectedTopic: null,
      currentIndex: 0,
      isFlipped: false,
      newStatus: '',
      selectedFilter: 'all',
      filteredCards: []
    }
  },
  computed: {
    currentCard() {
      return this.filteredCards[this.currentIndex] || null // Gibt die aktuelle Karte oder null zurück
    }
  },
  methods: {
    async fetchCards() {
      try {
        const response = await fetch('http://localhost:3001/cards')
        const data = await response.json()
        this.cards = data // Setze alle Karten

        // Hole die Details für jedes Modul, Tool und Thema
        for (let card of this.cards) {
          if (card.module && card.tool && card.topic) {
            const [moduleResponse, toolResponse, topicResponse] = await Promise.all([
              fetch(`http://localhost:3001/modules/${card.module.id}`),
              fetch(`http://localhost:3001/tools/${card.tool.id}`),
              fetch(`http://localhost:3001/topics/${card.topic.id}`)
            ])

            if (moduleResponse.ok && toolResponse.ok && topicResponse.ok) {
              const moduleData = await moduleResponse.json()
              const toolData = await toolResponse.json()
              const topicData = await topicResponse.json()

              // Füge die Module, Tools und Themen zu jeder Karte hinzu
              card.module = moduleData
              card.tool = toolData
              card.topic = topicData
            }
          }
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
      const topicResponse = await fetch('http://localhost:3001/topics')

      this.modules = await modulesResponse.json()
      this.tools = await toolsResponse.json()
      this.topics = await topicResponse.json()
    },
    applyFilter() {
      // Filtere die Karten basierend auf dem aktuellen Filter
      if (this.selectedFilter === 'all') {
        this.filteredCards = this.cards // Alle Karten anzeigen
      } else {
        this.filteredCards = this.cards.filter((card) => card.status === this.selectedFilter) // Gefilterte Karten
      }
      this.currentIndex = 0 // Setze den Index zurück
      this.isFlipped = false // Setze den Zustand für das Umdrehen der Karte zurück
    },
    /*updateAvailableTools() {
      // Filtere die verfügbaren Tools basierend auf dem ausgewählten Modul
      if (this.selectedModule) {
        this.availableTools = this.tools.filter((tool) => tool.moduleId === this.selectedModule)
      } else {
        this.availableTools = this.tools // Alle Tools anzeigen
      }
      this.selectedTool = null // Setze den ausgewählten Tool zurück
      this.updateAvailableTopics() // Aktualisiere die verfügbaren Themen
      this.updateCurrentCard() // Aktualisiere die angezeigten Karten
    },
    updateAvailableTopics() {
      // Filtere die verfügbaren Themen basierend auf dem ausgewählten Tool
      if (this.selectedTool) {
        this.availableTopics = this.topics.filter((topic) => topic.toolId === this.selectedTool)
      } else {
        this.availableTopics = this.topics // Alle Themen anzeigen
      }
      this.updateCurrentCard() // Aktualisiere die angezeigten Karten
    },*/
    updateCurrentCard() {
      this.currentIndex = 0 // Setze den Index zurück
      // Filtere die Karten basierend auf den ausgewählten IDs
      this.filteredCards = this.cards.filter(
        (card) =>
          (!this.selectedModule || card.moduleId === this.selectedModule) &&
          (!this.selectedTool || card.toolId === this.selectedTool) &&
          (!this.selectedTopic || card.topicId === this.selectedTopic)
      )

      // Wenn keine Karten gefunden wurden, zeige die gesamte Liste an
      if (this.filteredCards.length === 0) {
        this.filteredCards = this.cards
      }
    },
    flipCard() {
      this.isFlipped = !this.isFlipped // Umdrehen der Karte
    },
    prevCard() {
      if (this.currentIndex > 0) {
        this.currentIndex--
        this.isFlipped = false // Setze den Zustand zurück, wenn zur vorherigen Karte gewechselt wird
      }
    },
    nextCard() {
      if (this.currentIndex < this.filteredCards.length - 1) {
        this.currentIndex++
        this.isFlipped = false // Setze den Zustand zurück, wenn zur nächsten Karte gewechselt wird
      }
    },
    updateCardStatus() {
      if (this.currentCard) {
        this.currentCard.status = this.newStatus // Aktualisiere den Status der aktuellen Karte
        console.log(`Kartenstatus für ${this.currentCard.title} aktualisiert auf ${this.newStatus}`)
      }
    },
    prevCard() {
      if (this.currentIndex > 0) {
        this.currentIndex-- // Gehe zur vorherigen Karte
      }
    },
    nextCard() {
      if (this.currentIndex < this.filteredCards.length - 1) {
        this.currentIndex++ // Gehe zur nächsten Karte
      }
    }
  },
  mounted() {
    this.fetchCards() // Lade die Karten beim Mounten der Komponente
    this.fetchCategories() // Lade Module, Tools und Themen
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
}

.tab {
  display: inline-block;
  height: 370px;
  padding: 0.7em 2em; /* Innenabstand */
  color: var(--white); /* Schriftfarbe */
  background-color: var(--vibrant-purple); /* Hintergrundfarbe */
  border: 1px solid white;
  border-radius: 30px 30px 0 0; /* Nur oben gerundet */
  margin-right: -20px; /* Überlappung der Registerkarten */
  cursor: pointer; /* Zeiger-Cursor */
  transition: background-color 0.3s; /* Übergangseffekte */
  position: relative;
  z-index: 2;
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
  display: flex;
  height: 300px; /* Höhe der Karte */
  perspective: 1000px; /* Perspektive für 3D-Effekt */
  cursor: pointer; /* Zeiger-Cursor */
  position: relative; /* Positionierung für die Karte */
  margin-top: -300px; /* Negative Margin, um die Karte nach oben zu verschieben */
  z-index: 2; /* Niedrigerer z-index, damit die Karte hinter den Tabs liegt */
  box-shadow: 15px 1px 15px 1px rgba(0, 0, 0, 0.6);
}

.card-face {
  position: absolute;
  width: 100%;
  height: 100%;
  backface-visibility: hidden;
  transition: transform 0.6s;
  border: 2px solid var(--vibrant-purple);
}

.card-front,
.card-back {
  align-items: center; /* Inhalte horizontal zentrieren */
  padding: 1rem; /* Innenabstand */
}

.card-front {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly; /* Inhalte in der Mitte zentrieren */
  background-color: var(--thistle);
  color: black;
  padding: 1rem;
}

.card-back {
  display: flex;
  flex-direction: column;
  justify-content: space-evenly; /* Inhalte in der Mitte zentrieren */
  background-color: var(--champagne);
  color: var(--white);
  transform: rotateY(180deg);
  padding: 1rem;
}

.card-header {
  text-decoration: underline;
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
