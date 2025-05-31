<template>
  <div class="region-selector">
    <div class="mode-selector">
      <button 
        @click="setMode(false)"
        :class="{ active: !isExtremeMode }"
      >
        Mode Standard
      </button>
      <button 
        @click="setMode(true)"
        :class="{ active: isExtremeMode }"
      >
        Mode Extrême
      </button>
    </div>

    <div class="selection-section">
      <div class="form-group" v-if="!isExtremeMode">
        <label for="region">Région :</label>
        <select id="region" v-model="selectedRegion">
          <option disabled value="">Sélectionnez une région</option>
          <option 
            v-for="region in REGIONS" 
            :key="region.id"
            :value="region.id"
          >
            {{ region.name }} ({{ region.count }} Pokémon)
          </option>
        </select>
      </div>
      <div v-else class="extreme-info">
        <p>Mode Extrême : Trouvez <strong>tous les Pokémon</strong> de toutes les générations !</p>
      </div>

      <div class="form-group">
        <label for="difficulty">Difficulté :</label>
        <select 
          id="difficulty" 
          v-model="selectedDifficulty"
          v-if="!isExtremeMode"
        >
          <option disabled value="">Sélectionnez une difficulté</option>
          <option 
            v-for="level in DIFFICULTY_LEVELS" 
            :key="level.id"
            :value="level.id"
          >
            {{ level.name }} ({{ level.time !== Infinity ? level.time / 60 + ' min' : 'Illimité' }})
          </option>
        </select>
        <select 
          id="extreme-difficulty" 
          v-model="selectedDifficulty"
          v-else
        >
          <option disabled value="">Sélectionnez une difficulté</option>
          <option 
            v-for="level in EXTREME_LEVELS" 
            :key="level.id"
            :value="level.id"
          >
            {{ level.name }} ({{ level.time !== Infinity ? level.time / 60 + ' min' : 'Illimité' }})
          </option>
        </select>
      </div>
    </div>

    <button 
      class="start-button"
      @click="startGame"
      :disabled="!canStart"
    >
      Commencer
    </button>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';
import { REGIONS, DIFFICULTY_LEVELS, EXTREME_LEVELS } from '../data/pokemon';

export default defineComponent({
  name: 'RegionSelector',
  emits: ['start-game'],
  data() {
    return {
      REGIONS,
      DIFFICULTY_LEVELS,
      EXTREME_LEVELS,
      selectedRegion: '',
      selectedDifficulty: '',
      isExtremeMode: false,
    };
  },
  computed: {
    canStart(): boolean {
      // En mode extrême, seule la difficulté est nécessaire
      if (this.isExtremeMode) {
        return !!this.selectedDifficulty;
      }
      // En mode standard, région et difficulté sont nécessaires
      return !!this.selectedRegion && !!this.selectedDifficulty;
    }
  },
  methods: {
    setMode(isExtreme: boolean) {
      this.isExtremeMode = isExtreme;
      this.selectedRegion = '';
      this.selectedDifficulty = '';
    },
    startGame() {
      this.$emit('start-game', this.selectedRegion, this.selectedDifficulty, this.isExtremeMode);
    }
  }
});
</script>

<style scoped>
.region-selector {
  background-color: white;
  padding: 2rem;
  border-radius: 8px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  max-width: 600px;
  margin: 0 auto;
}

.mode-selector {
  display: flex;
  margin-bottom: 1.5rem;
  gap: 1rem;
}

.mode-selector button {
  flex: 1;
  padding: 0.75rem;
  border: none;
  border-radius: 4px;
  background-color: #ecf0f1;
  cursor: pointer;
  font-weight: bold;
  transition: all 0.2s;
}

.mode-selector button.active {
  background-color: #3498db;
  color: white;
}

.selection-section {
  margin-bottom: 1.5rem;
}

.form-group {
  margin-bottom: 1rem;
}

.form-group label {
  display: block;
  margin-bottom: 0.5rem;
  font-weight: bold;
  color: #2c3e50;
}

.form-group select {
  width: 100%;
  padding: 0.75rem;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-size: 1rem;
}

.extreme-info {
  background-color: #f8f9fa;
  padding: 1rem;
  border-radius: 4px;
  margin-bottom: 1rem;
  border-left: 4px solid #3498db;
}

.extreme-info p {
  margin: 0;
  color: #2c3e50;
}

.extreme-info strong {
  color: #e74c3c;
}

.start-button {
  width: 100%;
  padding: 1rem;
  background-color: #2ecc71;
  color: white;
  border: none;
  border-radius: 4px;
  font-size: 1.1rem;
  font-weight: bold;
  cursor: pointer;
  transition: background-color 0.2s;
}

.start-button:hover {
  background-color: #27ae60;
}

.start-button:disabled {
  background-color: #95a5a6;
  cursor: not-allowed;
}
</style>