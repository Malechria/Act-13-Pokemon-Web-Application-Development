<template>
  <div class="container">
    <h1>Buscador de Pokémon</h1>
    
    <div class="search-box">
      <input 
        v-model="searchQuery" 
        @keyup.enter="fetchPokemon"
        type="text" 
        placeholder="Ingresa el nombre del Pokémon..." 
      />
      <button @click="fetchPokemon" :disabled="loading">
        {{ loading ? 'Buscando...' : 'Buscar' }}
      </button>
    </div>

    <div v-if="error" class="error-message">
      {{ errorMessage }}
    </div>

    <div v-if="pokemon && !error" class="pokemon-card">
      <h2 class="pokemon-name">{{ pokemon.name }}</h2>
      
      <img 
        :src="pokemon.image" 
        :alt="`Imagen oficial de ${pokemon.name}`" 
        class="pokemon-image"
      />
      
      <div class="audio-player">
        <p>Grito:</p>
        <audio controls :src="pokemon.cry">
          Tu navegador no soporta el elemento de audio.
        </audio>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
import axios from 'axios';

// Estado de la App
const searchQuery = ref('');
const pokemon = ref(null);
const error = ref(false);
const errorMessage = ref('');
const loading = ref(false);

// Función para buscar el Pokémon
const fetchPokemon = async () => {
  if (!searchQuery.value.trim()) return;

  loading.value = true;
  error.value = false;
  pokemon.value = null;

  try {
    const query = searchQuery.value.toLowerCase().trim();
    const response = await axios.get(`https://pokeapi.co/api/v2/pokemon/${query}`);
    const data = response.data;

    pokemon.value = {
      name: data.name,
      image: data.sprites.other['official-artwork'].front_default,
      cry: data.cries.latest
    };
  } catch (err) {
    error.value = true;
    errorMessage.value = 'Pokémon no encontrado. Por favor, verifica el nombre e intenta de nuevo.';
  } finally {
    loading.value = false;
  }
};
</script>

<style scoped>
.container {
  max-width: 500px;
  margin: 0 auto;
  padding: 2rem;
  font-family: Arial, sans-serif;
  text-align: center;
}

h1 {
  color: #333;
}

.search-box {
  display: flex;
  gap: 10px;
  justify-content: center;
  margin-bottom: 2rem;
}

input {
  padding: 10px;
  font-size: 16px;
  border: 1px solid #ccc;
  border-radius: 4px;
  width: 60%;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: #4CAF50;
  color: white;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

button:disabled {
  background-color: #9e9e9e;
  cursor: not-allowed;
}

button:hover:not(:disabled) {
  background-color: #45a049;
}

.error-message {
  color: #d32f2f;
  background-color: #ffebee;
  padding: 15px;
  border-radius: 4px;
  margin-bottom: 1rem;
}

.pokemon-card {
  border: 1px solid #ddd;
  border-radius: 8px;
  padding: 20px;
  background-color: #f9f9f9;
  box-shadow: 0 4px 6px rgba(0,0,0,0.1);
}

.pokemon-name {
  text-transform: capitalize;
  margin-top: 0;
  color: #2c3e50;
  font-size: 2rem;
}

.pokemon-image {
  max-width: 250px;
  height: auto;
  margin: 10px 0;
}

.audio-player {
  margin-top: 15px;
}

.audio-player p {
  margin-bottom: 5px;
  font-weight: bold;
  color: #555;
}
</style>