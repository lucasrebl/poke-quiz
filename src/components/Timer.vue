<template>
  <div class="timer" :class="{ warning: isWarning, danger: isDanger }">
    {{ formattedTime }}
  </div>
</template>

<script lang="ts">
import { defineComponent } from 'vue';

export default defineComponent({
  name: 'Timer',
  props: {
    initialTime: {
      type: Number,
      required: true
    },
    isRunning: {
      type: Boolean,
      default: true
    },
    isTraining: {
      type: Boolean,
      default: false
    }
  },
  emits: ['time-up'],
  data() {
    return {
      currentTime: this.isTraining ? 0 : this.initialTime,
      timerInterval: null as number | null
    };
  },
  computed: {
    formattedTime(): string {
      if (this.initialTime === Infinity) {
        if (this.isTraining) {
          // Afficher le temps écoulé au format mm:ss
          const minutes = Math.floor(this.currentTime / 60);
          const seconds = this.currentTime % 60;
          return `${minutes}:${seconds.toString().padStart(2, '0')}`;
        }
        return '∞';
      }
      
      const minutes = Math.floor(this.currentTime / 60);
      const seconds = this.currentTime % 60;
      return `${minutes}:${seconds.toString().padStart(2, '0')}`;
    },
    isWarning(): boolean {
      return !this.isTraining && this.currentTime !== Infinity && this.currentTime <= 300; // 5 minutes
    },
    isDanger(): boolean {
      return !this.isTraining && this.currentTime !== Infinity && this.currentTime <= 60; // 1 minute
    }
  },
  watch: {
    isRunning(newVal) {
      if (newVal) {
        this.startTimer();
      } else {
        this.stopTimer();
      }
    }
  },
  mounted() {
    if (this.isRunning) {
      this.startTimer();
    }
  },
  beforeUnmount() {
    this.stopTimer();
  },
  methods: {
    startTimer() {
      this.stopTimer();
      
      this.timerInterval = window.setInterval(() => {
        if (this.isTraining) {
          // Mode entraînement: on incrémente le timer
          this.currentTime++;
        } else if (this.currentTime > 0 && this.currentTime !== Infinity) {
          // Mode normal: on décrémente le timer
          this.currentTime--;
        } else if (this.currentTime === 0 && !this.isTraining) {
          // Temps écoulé en mode normal
          this.stopTimer();
          this.$emit('time-up');
        }
      }, 1000);
    },
    stopTimer() {
      if (this.timerInterval) {
        clearInterval(this.timerInterval);
        this.timerInterval = null;
      }
    }
  }
});
</script>

<style scoped>
.timer {
  font-size: 1.5rem;
  font-weight: bold;
  font-family: monospace;
  color: #2c3e50;
  padding: 0.5rem 1rem;
  background-color: #ecf0f1;
  border-radius: 4px;
}

.timer.warning {
  color: #f39c12;
  background-color: #fef5e7;
}

.timer.danger {
  color: #e74c3c;
  background-color: #fdedec;
  animation: pulse 1s infinite;
}

@keyframes pulse {
  0% { opacity: 1; }
  50% { opacity: 0.7; }
  100% { opacity: 1; }
}
</style>