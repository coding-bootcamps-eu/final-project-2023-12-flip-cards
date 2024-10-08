<template>
  <div class="app-wrapper">
    <!-- Header -->
    <header>
      <HeaderComponent title="Create Your Card" msg="Fill in the details below" />
    </header>

    <!-- Input Form Section -->
    <main>
      <div class="card-wrapper">
        <!-- Card Wrapper for the form -->
        <form @submit.prevent="saveCard">
          <!-- Form for user input -->
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
              <input
                type="text"
                id="card-title"
                v-model="newCard.title"
                placeholder="Enter card title here"
                required
              />
            </p>
            <p>
              <textarea
                id="front-text"
                v-model="newCard.text_1"
                placeholder="Enter front text here"
                required
              ></textarea>
            </p>
            <p>
              <textarea
                id="back-text"
                v-model="newCard.text_2"
                placeholder="Enter back text here"
                required
              ></textarea>
            </p>
          </div>
          <!-- Button to save the card inside the form -->
          <button type="submit">Save Card</button>
        </form>
      </div>
    </main>

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
  name: 'InputView',
  components: {
    HeaderComponent,
    FooterComponent
  },
  data() {
    return {
      newCard: {
        id: null,
        title: '',
        text_1: '',
        text_2: '',
        status: '',
        date_last_practiced: '',
        times_practiced: 0,
        moduleId: '',
        toolId: '',
        topicId: ''
      },
      modules: [],
      tools: [],
      topics: [],
      cards: []
    }
  },

  mounted() {
    this.fetchModules()
    this.fetchTools()
    this.fetchTopics()
    this.fetchCards()
  },
  methods: {
    fetchModules() {
      fetch('http://localhost:3001/modules') // API endpoint for modules
        .then((response) => response.json())
        .then((data) => {
          this.modules = data // Store retrieved modules
        })
        .catch((error) => {
          console.error('Error fetching modules:', error)
        })
    },
    fetchTools() {
      fetch('http://localhost:3001/tools') // API endpoint for tools
        .then((response) => response.json())
        .then((data) => {
          this.tools = data // Store retrieved tools
        })
        .catch((error) => {
          console.error('Error fetching tools:', error)
        })
    },
    fetchTopics() {
      fetch('http://localhost:3001/topics') // API endpoint for topics
        .then((response) => response.json())
        .then((data) => {
          this.topics = data // Store retrieved topics
        })
        .catch((error) => {
          console.error('Error fetching topics:', error)
        })
    },
    fetchCards() {
      fetch('http://localhost:3001/cards') // API endpoint für Karten
        .then((response) => response.json())
        .then((data) => {
          this.cards = data // Speichern der bestehenden Karten
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },

    saveCard() {
      // set new id according to highest id +1
      const highestId = this.cards.reduce((max, card) => Math.max(max, card.id), 0)
      this.newCard.id = highestId + 1
      this.newCard.status = 'new'
      // Save the card by sending it to the API
      fetch('http://localhost:3001/cards', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.newCard) // Send the card data as JSON
      })
        .then((response) => response.json())
        .then((data) => {
          console.log('Card saved:', data)
          this.resetForm() // Reset the form after saving
        })
        .catch((error) => {
          console.error('Error saving the card:', error)
        })
    },
    resetForm() {
      // Reset the form fields
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
.app-wrapper {
  display: flex;
  flex-direction: column;
  min-height: 100vh; /* Full height of the viewport */
}

main {
  flex: 1; /* Make the main content grow to fill available space */
}

footer {
  background-color: var(--vibrant-purple); /* Background color for the footer */
  color: white;
  text-align: center;
  padding: 10px;
}

main {
  padding-top: 10px;
}

/* Card Wrapper */
.card-wrapper {
  background-color: white;
  border: 1px solid #ddd;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
  padding: 10px;
  margin-bottom: 20px;
}

/* Header Styles */
.card-header {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
  background-color: #e4d2f9;
}

input[type='text'],
textarea,
select {
  width: 100%;
  padding: 10px;
  margin: 5px 0;
  border: 1px solid #ccc;
  border-radius: 5px;
}

select {
  background-color: #e4d2f9;
  color: #333;
}

select:focus {
  border-color: #6a1cc3;
  outline: none;
}

textarea {
  height: 60px;
}

/* Placeholder Styles */
input::placeholder,
textarea::placeholder {
  font-family: Arial, sans-serif;
  font-size: 14px;
  color: #aaa;
}

/* Button Styles */
button {
  margin-top: 10px;
  padding: 10px 20px;
  background-color: var(--vibrant-purple);
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #5a0e94;
}
</style>
