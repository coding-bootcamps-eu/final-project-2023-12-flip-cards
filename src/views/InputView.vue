<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Create Your Card" msg="Fill in the details below" />
    </header>

    <!-- Input Form Section -->
    <main>
      <div class="card-wrapper">
        <!-- Card Wrapper für das Formular -->
        <form @submit.prevent="saveCard">
          <!-- Formular für die Eingaben -->
          <div class="card-header">
            <div class="input-group">
              <label for="module-select">Module:</label>
              <select id="module-select" v-model="newCard.moduleId" required>
                <option v-for="module in modules" :value="module.id" :key="module.id">
                  {{ module.name }}
                </option>
              </select>
            </div>
            <div class="input-group">
              <label for="tool-select">Tool:</label>
              <select id="tool-select" v-model="newCard.toolId" required>
                <option v-for="tool in tools" :value="tool.id" :key="tool.id">
                  {{ tool.name }}
                </option>
              </select>
            </div>
            <div class="input-group">
              <label for="topic-select">Topic:</label>
              <select id="topic-select" v-model="newCard.topicId" required>
                <option v-for="topic in topics" :value="topic.id" :key="topic.id">
                  {{ topic.name }}
                </option>
              </select>
            </div>
          </div>
          <div class="card-body">
            <p>
              <label for="card-title"></label>
              <input
                type="text"
                id="card-title"
                v-model="newCard.title"
                placeholder="Enter card title here"
                required
              />
            </p>
            <p>
              <label for="front-text"></label>
              <textarea
                id="front-text"
                v-model="newCard.text_1"
                placeholder="Enter front text here"
                required
              ></textarea>
            </p>
            <p>
              <label for="back-text"></label>
              <textarea
                id="back-text"
                v-model="newCard.text_2"
                placeholder="Enter back text here"
                required
              ></textarea>
            </p>
          </div>
        </form>
      </div>
      <button type="submit">Save Card</button>
      <!-- Button zum Speichern -->
    </main>

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
  name: 'InputView',
  components: {
    HeaderComponent,
    FooterComponent
  },
  data() {
    return {
      newCard: {
        title: '',
        moduleId: null,
        toolId: null,
        topicId: null,
        text_1: '',
        text_2: ''
      },
      modules: [], // Array für Module
      tools: [], // Array für Tools
      topics: [] // Array für Themen
    }
  },
  mounted() {
    this.fetchModules()
    this.fetchTools()
    this.fetchTopics()
  },
  methods: {
    fetchModules() {
      fetch('http://localhost:3001/modules') // API-Endpunkt für Module
        .then((response) => response.json())
        .then((data) => {
          this.modules = data // Speichern der abgerufenen Module
        })
        .catch((error) => {
          console.error('Fehler beim Abrufen der Module:', error)
        })
    },
    fetchTools() {
      fetch('http://localhost:3001/tools') // API-Endpunkt für Tools
        .then((response) => response.json())
        .then((data) => {
          this.tools = data // Speichern der abgerufenen Tools
        })
        .catch((error) => {
          console.error('Fehler beim Abrufen der Tools:', error)
        })
    },
    fetchTopics() {
      fetch('http://localhost:3001/topics') // API-Endpunkt für Themen
        .then((response) => response.json())
        .then((data) => {
          this.topics = data // Speichern der abgerufenen Themen
        })
        .catch((error) => {
          console.error('Fehler beim Abrufen der Themen:', error)
        })
    },
    saveCard() {
      // Sende die neue Karte an die API
      fetch('http://localhost:3001/cards', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.newCard) // Sende die neuen Kartendaten als JSON
      })
        .then((response) => response.json())
        .then((data) => {
          console.log('Karte gespeichert:', data)
          this.resetForm() // Setze das Formular zurück nach dem Speichern
        })
        .catch((error) => {
          console.error('Fehler beim Speichern der Karte:', error)
        })
    },
    resetForm() {
      // Setze das Formular zurück
      this.newCard = {
        title: '',
        moduleId: null,
        toolId: null,
        topicId: null,
        text_1: '',
        text_2: ''
      }
    }
  }
}
</script>

<style scoped>
/* Allgemeine Stile für die InputView-Komponente */
main {
  padding-top: 10px; /* Padding für den Hauptbereich */
}

/* Card Wrapper */
.card-wrapper {
  background-color: white; /* Hintergrundfarbe der Karte */
  border: 1px solid #ddd; /* Rahmen um die Karte */
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1); /* Schatten für die Karte */
  padding: 0px; /* Padding innerhalb der Karte */
  margin-bottom: 20px; /* Abstand zwischen den Karten */
}

/* Header-Stile */
.card-header {
  display: flex; /* Flexbox für die Anordnung der Eingabefelder */
  justify-content: space-between; /* Abstand zwischen den Eingabefeldern */
  margin-bottom: 0px; /* Abstand unter dem Header */
  background-color: #e4d2f9;
}

input[type='text'],
textarea,
select {
  width: 100%; /* Breite auf 100% setzen für die nebeneinander stehenden Felder */
  padding: 10px; /* Padding für die Eingabefelder */
  margin: 5px 0; /* Abstand oben und unten */
  border: 1px solid #ccc; /* Rahmen für die Eingabefelder */
  border-radius: 5px; /* Abgerundete Ecken */
}

select {
  background-color: #e4d2f9; /* Hintergrundfarbe */
  color: #333; /* Textfarbe */
}

select:focus {
  border-color: #6a1cc3; /* Beispiel: Setze die Rahmenfarbe auf einen lila Farbton */
  outline: none; /* Entferne den Standard-Outline */
}

/* Optional: Höhe für Textarea anpassen */
textarea {
  height: 60px; /* Beispielhöhe für Textarea */
}

/* Platzhalter-Stile */
input::placeholder,
textarea::placeholder {
  font-family: Arial, sans-serif; /* Setze die gewünschte Schriftart */
  font-size: 14px; /* Setze die gewünschte Schriftgröße */
  color: #aaa; /* Setze die gewünschte Schriftfarbe */
}

/* Button-Stile */
button {
  margin-bottom: 10px;
  padding: 10px 20px; /* Padding für den Button */
  background-color: var(--vibrant-purple); /* Hintergrundfarbe */
  color: white; /* Textfarbe */
  border: none; /* Kein Rahmen */
  border-radius: 5px; /* Abgerundete Ecken */
  cursor: pointer; /* Zeiger für den Cursor */
  transition: background-color 0.3s; /* Übergangseffekt */
}

button:hover {
  background-color: #5a0e94; /* Dunklere Farbe beim Hover */
}

/* Optional: Weitere Stile für das Layout */
</style>
