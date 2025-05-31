<template>
  <div class="pokemon-image">
    <img 
      :src="imageSrc"
      :alt="pokemonName"
      @error="handleImageError"
    />
    <div class="pokemon-name">{{ pokemonName }}</div>
    <div class="pokemon-id">#{{ formattedId }}</div>
  </div>
</template>

<script lang="ts">
import { defineComponent, computed } from 'vue';

export default defineComponent({
  name: 'PokemonImage',
  props: {
    pokemonId: {
      type: Number,
      required: true
    },
    pokemonName: {
      type: String,
      required: true
    }
  },
  setup(props) {
    const formattedId = computed(() => {
      return props.pokemonId.toString().padStart(3, '0');
    });
    
    // Pré-charger toutes les images de Pokémon dans un objet
    const images = import.meta.glob('@/assets/pokemon-images/*.png', { eager: true, import: 'default' }) as Record<string, string>;

    const getImagePath = (fileName: string) => {
      // Vite utilise des chemins relatifs à 'src'
      return images[`/src/assets/pokemon-images/${fileName}`];
    };

    const imageSrc = computed(() => {
      // Essayer d'abord avec le format 001.png
      const padded = props.pokemonId.toString().padStart(3, '0') + '.png';
      const simple = props.pokemonId + '.png';
      return (
        getImagePath(padded) ||
        getImagePath(simple) ||
        getImagePath('000.png')
      );
    });

    const handleImageError = (e: Event) => {
      const img = e.target as HTMLImageElement;
      img.src = getImagePath('000.png'); // Image par défaut
    };
    
    return {
      formattedId,
      imageSrc,
      handleImageError
    };
  }
});
</script>

<style scoped>
.pokemon-image {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: #f8f9fa;
  border-radius: 8px;
  padding: 0.5rem;
  transition: transform 0.2s, box-shadow 0.2s;
}

.pokemon-image:hover {
  transform: translateY(-5px);
  box-shadow: 0 5px 15px rgba(0, 0, 0, 0.1);
}

.pokemon-image img {
  width: 80px;
  height: 80px;
  object-fit: contain;
  image-rendering: pixelated;
}

.pokemon-name {
  font-weight: bold;
  margin-top: 0.5rem;
  color: #2c3e50;
  text-align: center;
  font-size: 0.9rem;
}

.pokemon-id {
  color: #7f8c8d;
  font-size: 0.8rem;
  margin-top: 0.2rem;
}
</style>