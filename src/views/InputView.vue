<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Create New Card" msg="Fill in the details below to add a new card." />
    </header>

    <!-- Main Content -->
    <main>
      <div class="form-container">
        <h2>Add a New Flip Card</h2>
        <form @submit.prevent="submitForm">
          <div class="form-group">
            <label for="module">Module:</label>
            <input type="text" v-model="newCard.moduleId" required placeholder="Enter module ID" />
          </div>

          <div class="form-group">
            <label for="tool">Tool:</label>
            <input type="text" v-model="newCard.toolId" required placeholder="Enter tool ID" />
          </div>

          <div class="form-group">
            <label for="topic">Topic:</label>
            <input type="text" v-model="newCard.topicId" required placeholder="Enter topic ID" />
          </div>

          <div class="form-group">
            <label for="title">Title:</label>
            <input type="text" v-model="newCard.title" required placeholder="Enter card title" />
          </div>

          <div class="form-group">
            <label for="text_1">Text Side 1:</label>
            <textarea
              v-model="newCard.text_1"
              required
              placeholder="Enter text for side 1"
            ></textarea>
          </div>

          <div class="form-group">
            <label for="text_2">Text Side 2:</label>
            <textarea
              v-model="newCard.text_2"
              required
              placeholder="Enter text for side 2"
            ></textarea>
          </div>

          <div class="form-group">
            <label for="timesPracticed">Times Practiced:</label>
            <input
              type="number"
              v-model="newCard.times_practiced"
              required
              placeholder="Number of times practiced"
            />
          </div>

          <div class="form-group">
            <label for="status">Status:</label>
            <select v-model="newCard.status" required>
              <option value="new">New</option>
              <option value="review needed">Review Needed</option>
              <option value="confident">Confident</option>
              <option value="archived">Archived</option>
            </select>
          </div>

          <button type="submit">Create Card</button>
        </form>
      </div>
    </main>

    <!-- Footer -->
    <footer>
      <FooterComponent msg="Footer component" />
    </footer>
  </div>
</template>

<script>
import HeaderComponent from '@/components/HeaderComponent.vue'
import MainComponent from '@/components/MainComponent.vue'
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  name: 'InputView',
  components: {
    HeaderComponent,
    MainComponent,
    FooterComponent
  },
  data() {
    return {
      newCard: {
        moduleId: '',
        toolId: '',
        topicId: '',
        title: '',
        text_1: '',
        text_2: '',
        times_practiced: 0,
        status: 'new'
      }
    }
  },
  methods: {
    submitForm() {
      // Assuming your API is running at localhost:3001 and expecting POST requests at /cards
      fetch('http://localhost:3001/cards', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify(this.newCard)
      })
        .then((response) => response.json())
        .then((data) => {
          alert('Card created successfully!')
          console.log('Card created:', data)
          // Optionally reset the form after submission
          this.resetForm()
        })
        .catch((error) => {
          console.error('Error creating card:', error)
        })
    },
    resetForm() {
      this.newCard = {
        moduleId: '',
        toolId: '',
        topicId: '',
        title: '',
        text_1: '',
        text_2: '',
        times_practiced: 0,
        status: 'new'
      }
    }
  }
}
</script>

<style scoped>
.form-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  border: 1px solid #ccc;
  border-radius: 5px;
  background-color: #f9f9f9;
}

h2 {
  text-align: center;
}

.form-group {
  margin-bottom: 15px;
}

.form-group label {
  display: block;
  font-weight: bold;
}

.form-group input,
.form-group textarea,
.form-group select {
  width: 100%;
  padding: 10px;
  margin-top: 5px;
  border: 1px solid #ccc;
  border-radius: 4px;
}

button {
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #007bff;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:hover {
  background-color: #0056b3;
}
</style>
