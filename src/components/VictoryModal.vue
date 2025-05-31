<template>
  <div class="modal-overlay" v-if="isOpen">
    <div class="modal">
      <h3>Félicitations !</h3>
      <p>Vous avez trouvé tous les {{ totalPokemon }} Pokémon !</p>
      <div class="time-info">
        <span v-if="isTraining">
          Temps mis : <strong>{{ formatTime(elapsedTime) }}</strong>
        </span>
        <span v-else>
          Temps restant : <strong>{{ formatTime(remainingTime) }}</strong>
        </span>
      </div>
      <div class="modal-actions">
        <button @click="onClose" class="confirm-button">Retour au menu</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'VictoryModal',
  props: {
    isOpen: {
      type: Boolean,
      required: true
    },
    totalPokemon: {
      type: Number,
      required: true
    },
    elapsedTime: {
      type: Number,
      default: 0
    },
    remainingTime: {
      type: Number,
      default: 0
    },
    isTraining: {
      type: Boolean,
      default: false
    }
  },
  emits: ['close'],
  methods: {
    onClose() {
      this.$emit('close');
    },
    formatTime(seconds: number): string {
      if (seconds === Infinity) return '∞';
      const minutes = Math.floor(seconds / 60);
      const secs = seconds % 60;
      return `${minutes}:${secs.toString().padStart(2, '0')}`;
    }
  }
});
</script>

<style scoped>
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1000;
}

.modal {
  background-color: white;
  padding: 2rem;
  border-radius: 8px;
  max-width: 500px;
  width: 90%;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.modal h3 {
  margin-bottom: 1rem;
  color: #2c3e50;
}

.modal p {
  margin-bottom: 1rem;
}

.time-info {
  margin-bottom: 1.5rem;
  padding: 0.75rem;
  background-color: #f8f9fa;
  border-radius: 4px;
  text-align: center;
  font-size: 1.1rem;
}

.time-info strong {
  color: #2ecc71;
  font-size: 1.2rem;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
}

.confirm-button {
  padding: 0.5rem 1rem;
  background-color: #2ecc71;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.cancel-button {
  padding: 0.5rem 1rem;
  background-color: #ecf0f1;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}
</style>