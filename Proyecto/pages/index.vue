<template>
  <div class="heroes">
    <div v-for="character in characters" :key="character.id" class="character-card">
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
  </div>
</template>

<script setup>
  import axios from "axios";
  const Hash = '70d3b7f3ca439f4687f5943770d5fc42';
  const publicKeys = '9bc0a75d38bd3ca28e1aaebfeb763510';
  
  const characters = ref([]);
  
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
  loadCharacter();
</script>