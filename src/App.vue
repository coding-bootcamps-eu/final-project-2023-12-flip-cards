<template>
  <header>
    <LogoComponent />
    <div id="app">
      <router-view />
      <!-- Hier wird der Inhalt der Route angezeigt -->
    </div>
  </header>

  <main>
    <h1>API is running on {{ apiUrl }}</h1>
    <div>
      <h1>Modules von der API</h1>
      <div v-if="dataModules">
        <pre>{{ dataModules }}</pre>
        <!-- Zeige die erhaltenen Daten an -->
      </div>
      <div v-else>
        <p>Lade Daten...</p>
      </div>
    </div>
    <div>
      <h1>Cards von der API</h1>
      <div v-if="dataCards">
        <pre>{{ dataCards }}</pre>
        <!-- Zeige die erhaltenen Daten an -->
      </div>
      <div v-else>
        <p>Lade Daten...</p>
      </div>
    </div>
  </main>
</template>

<script>
import LogoComponent from '@/components/LogoComponent.vue'
export default {
  data() {
    return {
      apiUrl: import.meta.env.VITE_API_URL,
      dataModules: null,
      dataCards: null
    }
  },
  mounted() {
    this.fetchData() // Daten abrufen, wenn die Komponente gemountet wird
  },
  methods: {
    async fetchData() {
      try {
        const responseModules = await fetch(`${this.apiUrl}modules`) // Hier können verschiedene Endpunkte eingesetzt werden`
        if (!responseModules.ok) {
          throw new Error('Netzwerkantwort war nicht ok')
        }
        const dataModules = await responseModules.json()
        this.dataModules = dataModules // Die von der API abgerufenen Daten werden in der data-Variable gespeichert
        const responseCards = await fetch(`${this.apiUrl}cards`) // Ersetze 'cards' durch den tatsächlichen Endpunkt für die Cards
        if (!responseCards.ok) {
          throw new Error('Netzwerkantwort für Cards war nicht ok')
        }
        const dataCards = await responseCards.json()
        this.dataCards = dataCards // Speichern der Karten-Daten
      } catch (error) {
        console.error('Fehler beim Abrufen der Daten:', error)
      }
    }
  },
  components: {
    LogoComponent
  }
}
</script>

<style scoped>
header {
  text-align: center;
  line-height: 1.5;
}

#logo {
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>

<style scoped>
header {
  line-height: 1.5;
}

.logo {
  display: block;
  margin: 0 auto 2rem;
}

@media (min-width: 1024px) {
  header {
    display: flex;
    place-items: center;
    padding-right: calc(var(--section-gap) / 2);
  }

  .logo {
    margin: 0 2rem 0 0;
  }

  header .wrapper {
    display: flex;
    place-items: flex-start;
    flex-wrap: wrap;
  }
}
</style>
