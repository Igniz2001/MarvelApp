<template>
    <div class="character-container">
      <div
        v-for="character in characters"
        :key="character.id"
        class="carta"
        @click="showCharacterDetails(character)"
      >
        <div class="linea-roja">
          <h2 class="nombres">{{ character.name }}</h2>
        </div>
        <div class="contenido">
          <div class="character-image-container">
            <img
              :src="character.thumbnail.path + '.' + character.thumbnail.extension"
              alt=""
              class="imagen"
            />
          </div>
        </div>
        
      </div>
      <div v-if="selctedcharacter" class="modal">
          <div v-if="selctedcharacter" class="modal-content">
            
            <!-- Contenido del modal -->
            <h2>{{ selctedcharacter.name }}</h2>
            <!-- Agrega aquí más detalles del personaje si es necesario -->
            <button class="close-button" @click="closeModal()">Cerrar</button>
          </div>
        </div>
    </div>
  </template>
  

  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const Hash = 'c922a0d15cfa7035874464492f823891';
  const publicKeys = '2dc534f2f94eb05c4aa8b32c092a6d02';
  const characters = ref([]);
  const selctedcharacter=ref(null);
  
  const loadCharacters = async () => {
    const url = `https://gateway.marvel.com:443/v1/public/characters?limit=100&ts=1&apikey=${publicKeys}&hash=${Hash}`;
    const { data } = await axios.get(url);
  
    const charactersWithImages = data.data.results.filter((character) => {
      return character.thumbnail.path !== 'http://i.annihil.us/u/prod/marvel/i/mg/b/40/image_not_available';
    });
  
    characters.value = charactersWithImages;
    console.log(charactersWithImages);
  };
  
  
  
  const showCharacterDetails = (character) => {
    console.log('Mostrando detalles para el personaje:', character.name);
    selctedcharacter.value=character
  };
  
  const closeModal = () => {
    selctedcharacter.value=null;
  };
  loadCharacters();
  </script>
  
  