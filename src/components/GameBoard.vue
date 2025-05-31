<template>
  <div class="game-board">
    <div class="game-header">
      <Timer 
        ref="timer"
        :initial-time="initialTime"
        :is-running="!isGameOver"
        :is-training="isTrainingMode"
        @time-up="handleTimeUp"
      />
      
      <div class="score">
        Trouvés : {{ foundPokemon.length }} / {{ totalPokemon }}
      </div>
    </div>

    <div class="progress-bar">
      <div 
        class="progress" 
        :style="{ width: `${(foundPokemon.length / totalPokemon) * 100}%` }"
      ></div>
    </div>

    <div class="game-content">
      <div class="input-container">
        <input 
          type="text" 
          v-model="currentGuess"
          @keyup.enter="checkGuess"
          :disabled="isGameOver"
          placeholder="Entrez le nom du Pokémon..."
          ref="guessInput"
        />
        <button @click="checkGuess" :disabled="isGameOver">Valider</button>
      </div>

      <div class="pokemon-grid-container">
        <div class="pokemon-grid">
          <PokemonImage 
            v-for="pokemon in foundPokemon"
            :key="pokemon.id"
            :pokemon-id="pokemon.id"
            :pokemon-name="pokemon.name"
          />
          <PokemonImage 
            v-if="isGameOver && !showVictoryModal"
            v-for="pokemon in sortedMissedPokemon"
            :key="pokemon.id"
            :pokemon-id="pokemon.id"
            :pokemon-name="pokemon.name"
            class="missed-pokemon"
          />
        </div>
      </div>
    </div>

    <div class="game-footer">
      <button @click="showAbandonModal = true" class="end-button">Abandonner</button>
      <button @click="showNewGameModal = true" class="restart-button">Nouvelle partie</button>
    </div>

    <!-- Modals -->
    <ConfirmationModal
      :is-open="showAbandonModal"
      title="Abandonner la partie"
      message="Êtes-vous sûr de vouloir abandonner ? Votre progression sera perdue."
      confirm-text="Abandonner"
      cancel-text="Continuer"
      @confirm="confirmAbandon"
      @cancel="showAbandonModal = false"
    />
    
    <ConfirmationModal
      :is-open="showNewGameModal"
      title="Nouvelle partie"
      message="Êtes-vous sûr de vouloir commencer une nouvelle partie ? Votre progression sera perdue."
      confirm-text="Confirmer"
      cancel-text="Annuler"
      @confirm="confirmNewGame"
      @cancel="showNewGameModal = false"
    />
    
    <VictoryModal
      :is-open="showVictoryModal"
      :total-pokemon="totalPokemon"
      :elapsed-time="timerElapsedTime"
      :remaining-time="timerRemainingTime"
      :is-training="isTrainingMode"
      @close="handleVictoryRestart"
    />

    <div v-if="keepVictoryDisplay && !showVictoryModal" class="victory-message">
      <h3>Félicitations !</h3>
      <p>Vous avez trouvé tous les {{ totalPokemon }} Pokémon !</p>
      <button @click="$emit('restart')" class="restart-button">Nouvelle partie</button>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { POKEMON_LIST } from '../data/pokemon';
import Timer from './Timer.vue';
import PokemonImage from './PokemonImage.vue';
import ConfirmationModal from './ConfirmationModal.vue';
import VictoryModal from './VictoryModal.vue';

interface Pokemon {
  id: number;
  name: string;
}

export default defineComponent({
  name: 'GameBoard',
  components: {
    Timer,
    PokemonImage,
    ConfirmationModal,
    VictoryModal
  },
  props: {
    region: {
      type: String,
      required: true
    },
    difficulty: {
      type: String,
      required: true
    },
    isExtreme: {
      type: Boolean,
      default: false
    }
  },
  emits: ['restart'],
  data() {
    return {
      pokemonToFind: [] as Pokemon[],
      foundPokemon: [] as Pokemon[],
      currentGuess: '',
      isGameOver: false,
      initialTime: 0,
      showAbandonModal: false,
      showNewGameModal: false,
      showVictoryModal: false,
      keepVictoryDisplay: false,
      timerKey: 0,
      timerElapsedTime: 0,
      timerRemainingTime: 0
    };
  },
  computed: {
    totalPokemon(): number {
      return this.pokemonToFind.length + this.foundPokemon.length;
    },
    remainingPokemon(): number {
      return this.pokemonToFind.length;
    },
    sortedMissedPokemon(): Pokemon[] {
      return [...this.pokemonToFind].sort((a, b) => a.id - b.id);
    },
    isTrainingMode(): boolean {
      return this.difficulty === 'training' || this.difficulty === 'extreme-training';
    }
  },
  watch: {
    remainingPokemon(newVal) {
      if (newVal === 0) {
        this.handleGameComplete();
      }
    }
  },
  created() {
    this.initializeGame();
  },
  mounted() {
    this.focusInput();
  },
  methods: {
    initializeGame() {
      this.isGameOver = false;
      this.currentGuess = '';
      this.foundPokemon = [];
      this.timerKey++;
      this.showVictoryModal = false;

      if (this.isExtreme) {
        this.pokemonToFind = [...POKEMON_LIST].map(p => ({ 
          id: p.id, 
          name: p.name 
        }));
      } else {
        this.pokemonToFind = [...POKEMON_LIST]
          .filter(p => p.region === this.region)
          .map(p => ({ 
            id: p.id, 
            name: p.name 
          }));
      }

      this.shuffleArray(this.pokemonToFind);
      this.setInitialTime();
    },

    setInitialTime() {
      if (this.isExtreme) {
        this.initialTime = this.difficulty === 'extreme-hardcore' ? 60 * 60 : Infinity;
        return;
      }

      switch (this.difficulty) {
        case 'easy': this.initialTime = 20 * 60; break;
        case 'medium': this.initialTime = 15 * 60; break;
        case 'hard': this.initialTime = 10 * 60; break;
        case 'training': this.initialTime = Infinity; break;
        default: this.initialTime = 20 * 60;
      }
    },

    shuffleArray(array: any[]) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
    },

    focusInput() {
      this.$nextTick(() => {
        const input = this.$refs.guessInput as HTMLInputElement;
        if (input) {
          input.focus();
        }
      });
    },

    checkGuess() {
      if (!this.currentGuess.trim() || this.isGameOver) return;

      const guess = this.currentGuess.trim().toLowerCase();
      const foundIndex = this.pokemonToFind.findIndex(
        p => p.name.toLowerCase() === guess
      );

      if (foundIndex !== -1) {
        const [foundPokemon] = this.pokemonToFind.splice(foundIndex, 1);
        this.foundPokemon.push(foundPokemon);
        this.foundPokemon.sort((a, b) => a.id - b.id);
      }

      this.currentGuess = '';
      this.focusInput();
    },

    handleTimeUp() {
      // En mode entraînement, on ne termine pas le jeu quand le temps est écoulé
      if (!this.isTrainingMode) {
        this.isGameOver = true;
      }
    },

    handleGameComplete() {
      this.isGameOver = true;
      
      // Capture le temps du Timer avant d'afficher la modal
      const timerComponent = this.$refs.timer as InstanceType<typeof Timer>;
      if (timerComponent) {
        if (this.isTrainingMode) {
          this.timerElapsedTime = timerComponent.currentTime;
          this.timerRemainingTime = 0;
        } else {
          this.timerRemainingTime = timerComponent.currentTime;
          this.timerElapsedTime = 0;
        }
      }
      
      this.showVictoryModal = true;
      this.keepVictoryDisplay = false;
    },

    confirmAbandon() {
      this.showAbandonModal = false;
      this.$emit('restart');
    },

    confirmNewGame() {
      this.showNewGameModal = false;
      this.$emit('restart');
    },

    handleVictoryRestart() {
      this.showVictoryModal = false;
      this.$emit('restart');
    }
  }
});
</script>

<style scoped>
.game-board {
  background-color: white;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  padding: 1.5rem;
  max-width: 1000px;
  margin: 0 auto;
  display: flex;
  flex-direction: column;
  height: 100%;
}

.game-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
  padding-bottom: 1rem;
  border-bottom: 1px solid #eee;
}

.score {
  font-size: 1.2rem;
  font-weight: bold;
  color: #2c3e50;
}

.progress-bar {
  height: 10px;
  background-color: #ecf0f1;
  border-radius: 5px;
  margin-bottom: 1.5rem;
  overflow: hidden;
}

.progress {
  height: 100%;
  background-color: #2ecc71;
  transition: width 0.3s;
}

.game-content {
  flex: 1;
  display: flex;
  flex-direction: column;
}

.input-container {
  display: flex;
  gap: 0.5rem;
  margin-bottom: 1.5rem;
}

.input-container input {
  flex: 1;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.input-container button {
  padding: 0 1.5rem;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.2s;
}

.input-container button:hover {
  background-color: #2980b9;
}

.input-container button:disabled {
  background-color: #95a5a6;
  cursor: not-allowed;
}

.pokemon-grid-container {
  flex: 1;
  overflow-y: auto;
  margin-bottom: 1rem;
}

.pokemon-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(120px, 1fr));
  gap: 1rem;
  padding: 0.5rem;
}

.game-footer {
  display: flex;
  justify-content: space-between;
  padding-top: 1rem;
  border-top: 1px solid #eee;
}

.game-footer button {
  padding: 0.75rem 1.5rem;
  border: none;
  border-radius: 4px;
  font-weight: bold;
  cursor: pointer;
  transition: all 0.2s;
}

.end-button {
  background-color: #e74c3c;
  color: white;
}

.end-button:hover {
  background-color: #c0392b;
}

.restart-button {
  background-color: #2ecc71;
  color: white;
}

.restart-button:hover {
  background-color: #27ae60;
}

.victory-message {
  background-color: #f8f9fa;
  padding: 1.5rem;
  border-radius: 8px;
  margin-bottom: 1rem;
  text-align: center;
  border-left: 4px solid #2ecc71;
}

.victory-message h3 {
  color: #2ecc71;
  margin-top: 0;
}

.victory-message p {
  margin-bottom: 1rem;
}

.missed-pokemon {
  opacity: 0.5;
  filter: grayscale(100%);
  position: relative;
}

.missed-pokemon::after {
  content: "Manqué";
  position: absolute;
  bottom: 4px;
  left: 50%;
  transform: translateX(-50%);
  background-color: rgba(231, 76, 60, 0.7);
  color: white;
  font-size: 0.7rem;
  padding: 2px 6px;
  border-radius: 4px;
}
</style>