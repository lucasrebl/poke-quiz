<template>
  <div class="app-container">
    <header class="app-header">
      <h1>Pokémon Quiz</h1>
      <p>Testez vos connaissances sur les Pokémon !</p>
    </header>

    <main class="app-main">
      <RegionSelector 
        v-if="!gameStarted"
        @start-game="startGame"
      />
      
      <GameBoard 
        v-else
        :region="selectedRegion"
        :difficulty="selectedDifficulty"
        :is-extreme="isExtremeMode"
        @game-over="handleGameOver"
        @restart="restartGame"
      />
    </main>

  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import RegionSelector from './components/RegionSelector.vue';
import GameBoard from './components/GameBoard.vue';

export default defineComponent({
  name: 'App',
  components: {
    RegionSelector,
    GameBoard
  },
  data() {
    return {
      gameStarted: false,
      selectedRegion: '',
      selectedDifficulty: '',
      isExtremeMode: false,
    };
  },
  methods: {
    startGame(region: string, difficulty: string, isExtreme: boolean) {
      this.selectedRegion = region;
      this.selectedDifficulty = difficulty;
      this.isExtremeMode = isExtreme;
      this.gameStarted = true;
    },
    handleGameOver(score: number, total: number) {
      alert(`Partie terminée ! Vous avez trouvé ${score} sur ${total} Pokémon.`);
    },
    restartGame() {
      this.gameStarted = false;
    }
  }
});
</script>

<style scoped>
.app-container {
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  font-family: 'Arial', sans-serif;
  background-color: #f5f5f5;
}

.app-header {
  background-color: #e74c3c;
  color: white;
  padding: 1rem;
  text-align: center;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
}

.app-main {
  flex: 1;
  padding: 2rem;
  max-width: 1200px;
  margin: 0 auto;
  width: 100%;
}
</style>