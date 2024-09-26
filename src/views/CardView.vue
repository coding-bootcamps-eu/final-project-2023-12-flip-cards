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
    <button
      class="nav-arrow right-arrow"
      @click="nextCard"
      :disabled="currentIndex === filteredCards.length - 1"
    >
      &#9654;
    </button>

    <!-- Karte anzeigen -->
    <div v-if="currentCard" class="card" :class="{ flipped: isFlipped }" @click="flipCard">
      <div class="card-face card-front">
        <div class="card-header">
          <h3>{{ currentCard.title }}</h3>
        </div>
        <div class="card-body">
          <p><strong>Side 1:</strong> {{ currentCard.text_1 }}</p>
        </div>
        <div class="card-footer">
          <p><strong>Status:</strong> {{ currentCard.status }}</p>
        </div>
      </div>

      <div class="card-face card-back">
        <div class="card-header">
          <h3>{{ currentCard.title }}</h3>
          <div class="card-body">
            <p><strong>Side 2:</strong> {{ currentCard.text_2 }}</p>
          </div>
          <div class="card-footer">
            <p><strong>Times Practiced:</strong> {{ currentCard.times_practiced }}</p>
          </div>
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

    <button class="nav-arrow left-arrow" @click="prevCard" :disabled="currentIndex === 0">
      &#9664;
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
      <FooterComponent msg="Cooding Bootcamps Europe" />
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
      topics: [], // Array für Themen
      availableTools: [], // Verfügbare Tools basierend auf dem ausgewählten Modul
      availableTopics: [], // Verfügbare Themen basierend auf dem ausgewählten Tool
      currentTab: 0 // Aktueller Tab-Index
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
    updateAvailableTools() {
      // Filtere die verfügbaren Tools basierend auf dem ausgewählten Modul
      if (this.selectedModule) {
        this.availableTools = this.tools.filter((tool) => tool.moduleId === this.selectedModule)
      } else {
        this.availableTools = this.tools // Alle Tools anzeigen, wenn kein Modul ausgewählt ist
      }
      this.selectedTool = null // Setze den ausgewählten Tool zurück
      this.updateAvailableTopics() // Aktualisiere die verfügbaren Themen
    },
    updateAvailableTopics() {
      // Filtere die verfügbaren Themen basierend auf dem ausgewählten Tool
      if (this.selectedTool) {
        this.availableTopics = this.topics.filter((topic) => topic.toolId === this.selectedTool)
      } else {
        this.availableTopics = this.topics // Alle Themen anzeigen, wenn kein Tool ausgewählt ist
      }
    },
    updateCurrentCard() {
      this.currentIndex = 0 // Setze den Index zurück

      // Filtere die Karten basierend auf den ausgewählten IDs
      this.filteredCards = this.cards.filter(
        (card) =>
          (!this.selectedModule || card.moduleId === this.selectedModule) &&
          (!this.selectedTool || card.toolId === this.selectedTool) &&
          (!this.selectedTopic || card.topicId === this.selectedTopic)
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
