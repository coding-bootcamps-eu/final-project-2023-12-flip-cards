<template>
  <div>
    <!-- Header -->
    <header>
      <HeaderComponent title="Card Statistics" msg="Track your progress" />
    </header>

    <!-- Main Section -->
    <main>
      <div class="stats-container">
        <h3>Flip Cards Statistics</h3>

        <!-- Practice and Learning Statistics -->
        <div class="stat-buttons">
          <button @click="filterBy('today', 'practiced')">Practiced Today</button>
          <button @click="filterBy('today', 'learned')">Learned Today</button>
          <button @click="filterBy('week', 'practiced')">Practiced This Week</button>
          <button @click="filterBy('week', 'learned')">Learned This Week</button>
          <button @click="filterBy('month', 'practiced')">Practiced This Month</button>
          <button @click="filterBy('month', 'learned')">Learned This Month</button>
        </div>

        <!-- Display filtered results -->
        <div v-if="filteredCards.length === 0">No cards for the selected category.</div>
        <div v-else>
          <h4>Results for: {{ filterText }}</h4>
          <ul>
            <li v-for="card in filteredCards" :key="card.id">
              <strong>{{ card.title }}</strong> ({{ card.status }})
            </li>
          </ul>
        </div>
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
import FooterComponent from '@/components/FooterComponent.vue'

export default {
  data() {
    return {
      cards: [],
      filteredCards: [],
      filterText: ''
    }
  },
  components: {
    HeaderComponent,
    FooterComponent
  },
  methods: {
    fetchCards() {
      fetch('http://localhost:3001/cards')
        .then((response) => response.json())
        .then((data) => {
          this.cards = data
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    filterBy(period, type) {
      const currentDate = new Date()
      let filterCondition = null

      switch (period) {
        case 'today':
          filterCondition = (cardDate) => cardDate.toDateString() === currentDate.toDateString()
          this.filterText = `${type === 'practiced' ? 'Practiced' : 'Learned'} Today`
          break
        case 'week':
          const weekStart = new Date(
            currentDate.setDate(currentDate.getDate() - currentDate.getDay())
          )
          filterCondition = (cardDate) => cardDate >= weekStart
          this.filterText = `${type === 'practiced' ? 'Practiced' : 'Learned'} This Week`
          break
        case 'month':
          const monthStart = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1)
          filterCondition = (cardDate) => cardDate >= monthStart
          this.filterText = `${type === 'practiced' ? 'Practiced' : 'Learned'} This Month`
          break
        default:
          filterCondition = null
      }

      if (filterCondition) {
        this.filteredCards = this.cards.filter((card) => {
          const cardDate = new Date(card.timestamp) // assuming cards have a 'timestamp'
          return filterCondition(cardDate) && card.type === type
        })
      }
    }
  },
  mounted() {
    this.fetchCards()
  }
}
</script>

<style scoped>
.stats-container {
  background-color: white;
  padding: 20px;
  border-radius: 8px;
  text-align: center;
  color: #6a1cc3; /* Vibrant Purple */
}

h2 {
  color: #fdba74; /* Peach / Light Orange */
  font-size: 24px;
  margin-bottom: 20px;
}

.stat-box {
  background-color: #6a1cc3; /* Vibrant Purple */
  color: #ffffff; /* White */
  padding: 15px;
  margin: 10px 0;
  border-radius: 8px;
  font-size: 18px;
}

.stat-box p {
  margin: 0;
}

.stat-buttons {
  display: flex;
  flex-direction: column; /* Vertikale Anordnung */
  align-items: center; /* Zentriert die Buttons horizontal */
  margin-bottom: 20px; /* Optional: Abstand nach unten */
}

.stat-buttons button {
  margin: 5px 0; /* Optional: Abstand zwischen den Buttons */
}
</style>
