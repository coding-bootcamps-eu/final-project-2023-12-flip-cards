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
        <p v-if="currentCard">{{ currentCard.toolName }}</p>
        <h3>{{ currentCard.title }}</h3>
        <p>{{ currentCard.text_1 }}</p>
        <div class="card-footer">
          <p><strong>This card is currently on stack:</strong> {{ currentCard.status }}</p>
        </div>
      </div>
      <div class="card-face card-back">
        <p><strong>Tool:</strong> {{ currentCard.tool }}</p>
        <h3>Title: {{ currentCard.title }}</h3>
        <p>{{ currentCard.text_2 }}</p>
        <p>
          <strong>You have practised this card {{ currentCard.times_practiced }} times!</strong>
        </p>
      </div>
    </div>

    <button
      class="nav-arrow right-arrow"
      @click="nextCard"
      :disabled="currentIndex === filteredCards.length - 1"
    >
      &#9654;
    </button>

    <!-- Filter Dropdown -->
    <div class="filter-section">
      <label for="filter">Select stack:</label>
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
      <h4>Move card to stack</h4>
      <label>
        <input type="radio" v-model="newStatus" value="new" @change="updateCardStatus" />
        New
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="review needed" @change="updateCardStatus" />
        Review Needed
      </label>
      <label>
        <input type="radio" v-model="newStatus" value="confident" @change="updateCardStatus" />
        Confident
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
      const card = this.filteredCards[this.currentIndex] || null // Gibt die aktuelle Karte oder null zurück
      return card
    }
  },
  methods: {
    async fetchCards() {
      try {
        // Abrufen der Karten
        const cardResponse = await fetch('http://localhost:3001/cards') // Ersetze durch die richtige URL
        // Abrufen der Tools
        const toolResponse = await fetch('http://localhost:3001/tools') // Ersetze durch die richtige URL

        // Überprüfen, ob die Antworten OK sind
        if (!cardResponse.ok || !toolResponse.ok) {
          throw new Error('Fehler beim Abrufen der Daten')
        }

        // Konvertiere die Antworten in JSON
        const cards = await cardResponse.json()
        const tools = await toolResponse.json()

        // Füge die Toolnamen zu den Karten hinzu
        this.cards = cards.map((card) => {
          const tool = tools.find((tool) => tool.id === card.toolId)
          return {
            ...card,
            toolName: tool ? tool.name : 'Unbekannt' // Füge den Toolnamen hinzu
          }
        })

        // Aktualisiere die aktuelle Karte
        this.updateCurrentCard()
      } catch (error) {
        console.error('Fehler:', error)
        // Benutzerbenachrichtigung hinzufügen??
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

      // Setze den aktuellen Index auf die erste Karte im gefilterten Array
      this.currentIndex = this.filteredCards.length > 0 ? 0 : -1 // -1, wenn keine Karten vorhanden sind
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
  margin-top: 2rem;
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
  color: var(--black);
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
  text-align: center;
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

/* Responsive Design for Mobile Landscape View */
@media (max-width: 600px) and (orientation: landscape) {
  header,
  .dropdown-container,
  .filter-section,
  .status-update {
    display: none; /* Hide these sections */
  }

  .card-container {
    height: 100vh; /* Full height for the card container */
    justify-content: center; /* Center the card vertically */
    align-items: center; /* Center the card horizontally */
    position: relative; /* Allow for absolute positioning of buttons */
  }

  .card {
    width: 90vw; /* Make the card width responsive */
    height: 60vh; /* Set height of the card */
    margin: 0; /* Remove margins */
    box-shadow: none; /* Remove shadow for a cleaner look */
  }

  .nav-arrow {
    font-size: 2rem; /* Increase arrow size */
    position: absolute; /* Position arrows relative to card */
    top: 50%;
    transform: translateY(-50%); /* Center arrows vertically */
  }

  .left-arrow {
    left: 20px; /* Position left arrow */
  }

  .right-arrow {
    right: 20px; /* Position right arrow */
  }

  .nav-arrow:disabled {
    color: #ccc; /* Disabled button styling */
    cursor: not-allowed; /* Change cursor for disabled state */
  }
}
</style>
