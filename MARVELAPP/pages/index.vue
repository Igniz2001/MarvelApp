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
        <div :id="'modal-' + character.id" class="modal">
        <div class="modal-content">
          <span class="close" @click="closeModal(character.id)">&times;</span>
          <!-- Contenido del modal -->
          <h2>{{ character.name }}</h2>
          <!-- Agrega aquí más detalles del personaje si es necesario -->
        </div>
      </div>
      </div>
    </div>
  </template>
  
  <style scoped>
  .character-container {
    display: flex;
    flex-wrap: wrap;
  }
  
  .carta {
    width: 23%; /* Ajusta el ancho para que haya 4 tarjetas por fila */
    margin: 10px;
    padding: 10px;
    border: 1px solid #ccc;
    box-sizing: border-box;
  }
  
  .linea-roja {
    border-bottom: 2px solid red;
    margin-bottom: 10px;
    padding-bottom: 5px;
  }
  
  .imagen {
    width: 100%; /* Haz que la imagen ocupe el 100% del contenedor */
    height: 200px; /* Ajusta la altura según tus preferencias */
    object-fit: cover; /* Ajusta la forma en que se muestra la imagen */
  }

  /* Agrega estilos para el modal */
.modal {
  display: none;
  position: fixed;
  z-index: 1;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgb(0, 0, 0);
  background-color: rgba(0, 0, 0, 0.4);
  padding-top: 60px;
}

.modal-content {
  background-color: #fefefe;
  margin: 5% auto;
  padding: 20px;
  border: 1px solid #888;
  width: 80%;
}

.close {
  color: #aaa;
  float: right;
  font-size: 28px;
  font-weight: bold;
}

.close:hover,
.close:focus {
  color: black;
  text-decoration: none;
  cursor: pointer;
}

  </style>
  
  <script setup>
  import { ref } from 'vue';
  import axios from 'axios';
  
  const Hash = 'c922a0d15cfa7035874464492f823891';
  const publicKeys = '2dc534f2f94eb05c4aa8b32c092a6d02';
  const characters = ref([]);
  
  const loadCharacters = async () => {
    const url = `https://gateway.marvel.com:443/v1/public/characters?limit=100&ts=1&apikey=${publicKeys}&hash=${Hash}`;
    const { data } = await axios.get(url);
  
    const charactersWithImages = data.data.results.filter((character) => {
      return character.thumbnail.path !== 'http://i.annihil.us/u/prod/marvel/i/mg/b/40/image_not_available';
    });
  
    characters.value = charactersWithImages;
    console.log(charactersWithImages);
  };
  
  loadCharacters();

  const showCharacterDetails = (character) => {
    const modal = document.getElementById('modal-' + character.id);
    modal.style.display = 'block';
  };

  const closeModal = (characterId) => {
    const modal = document.getElementById('modal-' + characterId);
    modal.style.display = 'none';
  };

  
  </script>
  