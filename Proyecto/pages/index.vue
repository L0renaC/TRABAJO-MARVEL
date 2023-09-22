<template>
  <div class="heroes">
    <div v-for="character in characters" :key="character.id" class="character-card" @click="showCharacterDetails(character)">
      <div class="character-header">
        <h2 class="character-name">{{ character.name }}</h2>
      </div>
      <div class="character-content">
        <div class="character-image-container">
          <img
            :src="character.thumbnail.path + '.' + character.thumbnail.extension"
            alt=""
            class="character-image"
          >
        </div>
      </div>
      
    </div>
    <div v-if="selectedCharacter" class="opacidad" @click="closeDialog">
    <div v-if="selectedCharacter" class="modal">
      <div class="modal-open">
      <button @click="closeDialog" class="close-button">Cerrar</button>
      <h2>{{ selectedCharacter.name }}</h2>
      <img class="peque" :src="selectedCharacter.thumbnail.path + '.' + selectedCharacter.thumbnail.extension" alt="">
      <p>{{ selectedCharacter.description }}</p>
      <p>Cantidad de comics: {{ selectedCharacter.comics.available }}</p>
      <p>Cantidad de series: {{ selectedCharacter.series.available }}</p>
      <p>Cantidad de stories: {{ selectedCharacter.stories.available }}</p>
      <p>Cantidad de events: {{ selectedCharacter.events.available }}</p>
      <h3>Primeras tres series:</h3>
      <ul>
        <li v-for="series in selectedCharacter.series.items.slice(0, 3)" :key="series.name">{{ series.name }}</li>
      </ul>
    </div>
    </div>
    </div>
  </div>
</template>

<script setup>
import { ref } from 'vue';
  import axios from "axios";
  const Hash = '70d3b7f3ca439f4687f5943770d5fc42';
  const publicKeys = '9bc0a75d38bd3ca28e1aaebfeb763510';
  
  const characters = ref([]);
  const selectedCharacter = ref(null);
  const loadCharacter = async ()=>{
    const url = `https://gateway.marvel.com:443/v1/public/characters?limit=100&ts=1&apikey=${publicKeys}&hash=${Hash}`;
    const { data } = await axios.get(url);
    console.log(data.data.results)
    const charactersWithImage = data.data.results.filter(character => {
      return character.thumbnail.path != "http://i.annihil.us/u/prod/marvel/i/mg/b/40/image_not_available"
    });
    characters.value=charactersWithImage;
    console.log(charactersWithImage)
    
  }
  const showCharacterDetails = (character) => {
  selectedCharacter.value = character;
  console.log("sapo")
}

const closeDialog = () => {
  selectedCharacter.value = null;
}
  loadCharacter();
</script>