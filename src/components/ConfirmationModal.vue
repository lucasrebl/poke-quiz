<template>
  <div class="modal-overlay" v-if="isOpen">
    <div class="modal">
      <h3>{{ title }}</h3>
      <p>{{ message }}</p>
      <div class="modal-actions">
        <button @click="onConfirm" class="confirm-button">{{ confirmText }}</button>
        <button @click="onCancel" class="cancel-button">{{ cancelText }}</button>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'ConfirmationModal',
  props: {
    isOpen: {
      type: Boolean,
      required: true
    },
    title: {
      type: String,
      default: 'Confirmation'
    },
    message: {
      type: String,
      default: 'Êtes-vous sûr de vouloir effectuer cette action ?'
    },
    confirmText: {
      type: String,
      default: 'Confirmer'
    },
    cancelText: {
      type: String,
      default: 'Annuler'
    }
  },
  emits: ['confirm', 'cancel'],
  methods: {
    onConfirm() {
      this.$emit('confirm');
    },
    onCancel() {
      this.$emit('cancel');
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
  margin-bottom: 1.5rem;
}

.modal-actions {
  display: flex;
  justify-content: flex-end;
  gap: 1rem;
}

.confirm-button {
  padding: 0.5rem 1rem;
  background-color: #e74c3c;
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