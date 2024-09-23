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

        <!-- Display counts for today, this week, this month, and by status -->
        <div class="stat-box">
          <p><strong>Cards Practiced Today:</strong> {{ counts.today }}</p>
          <p><strong>Cards Practiced This Week:</strong> {{ counts.week }}</p>
          <p><strong>Cards Practiced This Month:</strong> {{ counts.month }}</p>
          <p><strong>Cards Needing Review:</strong> {{ counts.reviewNeeded }}</p>
          <p><strong>Confident Cards:</strong> {{ counts.confident }}</p>
        </div>

        <!-- Practice and Learning Statistics -->
        <div class="stat-buttons">
          <button @click="filterBy('today')">View Practiced Today</button>
          <button @click="filterBy('week')">View Practiced This Week</button>
          <button @click="filterBy('month')">View Practiced This Month</button>
        </div>

        <!-- Status Filter Buttons -->
        <div class="stat-buttons">
          <button @click="filterByStatus('review_needed')">View Cards Needing Review</button>
          <button @click="filterByStatus('confident')">View Confident Cards</button>
        </div>

        <!-- Display filtered results -->
        <div v-if="filteredCards.length === 0">No cards for the selected category.</div>
        <div v-else>
          <h4>Results for: {{ filterText }}</h4>
          <ul>
            <li v-for="card in filteredCards" :key="card.id">
              <strong>{{ card.title }}</strong> - Status: {{ card.status }} (Times Practiced:
              {{ card.times_practiced }})
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
      cards: [], // All cards from the API
      filteredCards: [], // Cards filtered based on the user's selection
      filterText: '', // Text indicating the current filter
      counts: {
        // Store counts for each period and status
        today: 0,
        week: 0,
        month: 0,
        reviewNeeded: 0,
        confident: 0
      }
    }
  },
  components: {
    HeaderComponent,
    FooterComponent
  },
  methods: {
    fetchCards() {
      // Fetch cards from the API
      fetch('http://localhost:3001/cards')
        .then((response) => response.json())
        .then((data) => {
          this.cards = data
          this.updateCounts()
        })
        .catch((error) => {
          console.error('Error fetching cards:', error)
        })
    },
    updateCounts() {
      // Calculate counts for today, this week, this month, and by status
      const currentDate = new Date()

      // Get the start of the week and month
      const weekStart = new Date(currentDate.setDate(currentDate.getDate() - currentDate.getDay()))
      const monthStart = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1)

      // Reset counts
      this.counts.today = 0
      this.counts.week = 0
      this.counts.month = 0
      this.counts.reviewNeeded = 0
      this.counts.confident = 0

      // Loop through cards and count based on last practice date and status
      this.cards.forEach((card) => {
        const cardDate = new Date(card.date_last_practiced)

        if (cardDate.toDateString() === new Date().toDateString()) {
          this.counts.today++
        }

        if (cardDate >= weekStart) {
          this.counts.week++
        }

        if (cardDate >= monthStart) {
          this.counts.month++
        }

        // Count based on card status
        if (card.status === 'review_needed') {
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
          // Filter cards practiced today
          filterCondition = (cardDate) => cardDate.toDateString() === currentDate.toDateString()
          this.filterText = 'Practiced Today'
          break
        case 'week':
          // Filter cards practiced this week
          const weekStart = new Date(
            currentDate.setDate(currentDate.getDate() - currentDate.getDay())
          )
          filterCondition = (cardDate) => cardDate >= weekStart
          this.filterText = 'Practiced This Week'
          break
        case 'month':
          // Filter cards practiced this month
          const monthStart = new Date(currentDate.getFullYear(), currentDate.getMonth(), 1)
          filterCondition = (cardDate) => cardDate >= monthStart
          this.filterText = 'Practiced This Month'
          break
        default:
          filterCondition = null
      }

      if (filterCondition) {
        this.filteredCards = this.cards.filter((card) => {
          const cardDate = new Date(card.date_last_practiced)
          return filterCondition(cardDate)
        })
      }
    },
    filterByStatus(status) {
      // Filter cards based on their status (review_needed or confident)
      this.filteredCards = this.cards.filter((card) => card.status === status)

      if (status === 'review_needed') {
        this.filterText = 'Cards Needing Review'
      } else if (status === 'confident') {
        this.filterText = 'Confident Cards'
      }
    }
  },
  mounted() {
    this.fetchCards() // Fetch cards and calculate initial counts
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

h3 {
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
  flex-direction: column;
  align-items: center;
  margin-bottom: 20px;
}

.stat-buttons button {
  margin: 5px 0;
}
</style>
