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

    <div v-if="selectedCharacter" class="opacidad" @click="closeDialog($event)" id="opacidad">
      <transition name="fade" @after-leave="removeModal" v-if="isDialogOpen">
        <div class="modal" :class="{'modal-fadeclosed': isClosing}">
        <div class="modal-open">
          <div class="modal-header">
            <h2 class="character-name" style="font-size: 30px;">{{ selectedCharacter.name }}</h2>
          </div>
          <div class="div-info"></div>
          <img class="character-image" :src="selectedCharacter.thumbnail.path + '.' + selectedCharacter.thumbnail.extension" alt="">
          <h3 class="textod" style="margin-top: 60px" >Descripción:</h3>
          <div class="texto" :class="{ 'text-right': !selectedCharacter.description }" style="margin-top: 60px;">
            <p>
              {{ selectedCharacter.description || 'No tiene Descripción' }}
            </p>
          </div>
          <div class="column-container">
          <div class="column">
            <p><b>Cantidad de comics:</b> {{ selectedCharacter.comics.available }}</p>
            <p><b>Cantidad de series:</b> {{ selectedCharacter.series.available }}</p>
            <p><b>Cantidad de stories:</b> {{ selectedCharacter.stories.available }}</p>
            <p><b>Cantidad de events:</b> {{ selectedCharacter.events.available }}</p>
          </div>
          <div class="column">
            <h3>Primeras tres series:</h3>
            <ul>
              <li v-if="selectedCharacter.series.items.length === 0">Este personaje no tiene series</li>
              <li v-for="series in selectedCharacter.series.items.slice(0, 3)" :key="series.name">{{ series.name }}</li>
            </ul>
          </div>
        </div>
          <button @click="closeDialog($event)" class="close-button character-name" id="close-button">Regresar</button> 
        </div>
        </div>
      </transition>
    </div>
  </div>
</template>


<script setup>
  import { ref } from 'vue';
  import axios from "axios";
  const Hash = '70d3b7f3ca439f4687f5943770d5fc42';
  const publicKeys = '9bc0a75d38bd3ca28e1aaebfeb763510';
  const isDialogOpen = ref(false);
  const isClosing = ref(false);
  const characters = ref([]);
  const openDialogs = ref([]);
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
  isDialogOpen.value = true;
  }

  const closeDialog = (event) => {
    if (event.target.id === "close-button" || event.target.id === "opacidad") {
      isClosing.value = true;
      setTimeout(() => {
        if (openDialogs.value.length === 0) {
          isDialogOpen.value = false; 
          document.body.style.overflow = "auto"; 
          const opacidad = document.getElementById("opacidad");
          if (opacidad) {
            opacidad.style.display = "none";
          }
        }
        isClosing.value = false;
        selectedCharacter.value = null;
      }, 500);
    } 
  };

    loadCharacter();
</script>