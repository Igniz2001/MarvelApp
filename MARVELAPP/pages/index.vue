<template>
  <div class="character-container">
    <div v-for="character in characters" :key="character.id" class="carta" @click="showCharacterDetails(character)">
      <div class="linea-roja">
        <h2 align="center" class="nombres">{{ character.name }}</h2>
      </div>
      <div class="contenido">
        <div class="character-image-container">
          <img :src="character.thumbnail.path + '.' + character.thumbnail.extension" alt="" class="imagen" />
        </div>
      </div>

    </div>
    <div v-if="selctedcharacter" class="modal">
      <div class="modal-content">
        <center>
          <h1>{{ selctedcharacter.name }}</h1>
          <div class="selctedcharacter-image-container">
            <img :src="selctedcharacter.thumbnail.path + '.' + selctedcharacter.thumbnail.extension" alt=""
              class="imagen" />
          </div>
          <div class="description-validator" v-if="!selctedcharacter.description">
            <h3>Description not available yet</h3>
          </div>
          <div v-else>
            <p>{{ selctedcharacter.description }}</p>
          </div>
          <table>
            <tr>
              <td>
                <h3>number of comics: {{ selctedcharacter.comics.available }}</h3>
              </td>
              <td>
                <h3>number of series: {{ selctedcharacter.series.available }}</h3>
              </td>
            </tr>
            <tr>
              <td>
                <h3>number of stories: {{ selctedcharacter.stories.available }}</h3>
              </td>
              <td>
                <h3>number of events: {{ selctedcharacter.events.available }}</h3>
              </td>
            </tr>
          </table>
        </center>
        <h2 align="center">First 3 series where he/she appears:</h2>
        <ul>
          <template v-for="(seriesItem, index) in selctedcharacter.series.items" :key="seriesItem.resourceURI">
            <!-- Mostrar solo las primeras 3 series -->
            <li v-if="index < 3">
              {{ seriesItem.name }}
            </li>
          </template>
        </ul>
        <br>
        <br>
        <center>
          <button class="close-button" @click="closeModal">
            <img src="https://media.tenor.com/8D4CNzYT7i0AAAAi/snap-the-snap.gif" alt="Snap GIF" class="gif-hover">
          </button>
        </center>
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
const selctedcharacter = ref(null);

const loadCharacters = async () => {
  const url = `https://gateway.marvel.com:443/v1/public/characters?limit=100&ts=1&apikey=${publicKeys}&hash=${Hash}`;
  const { data } = await axios.get(url);

  const charactersWithImages = data.data.results
    .filter((character) => {
      return character.thumbnail.path !== 'http://i.annihil.us/u/prod/marvel/i/mg/b/40/image_not_available';
    })
    .map(async (character) => {
      const characterDetails = await axios.get(
        `https://gateway.marvel.com:443/v1/public/characters/${character.id}?apikey=${publicKeys}&hash=${Hash}`
      );
      character.description = characterDetails.data.data.results[0].description;
      return character;
    });

  characters.value = await Promise.all(charactersWithImages);
  console.log(characters.value);
};

const showCharacterDetails = (character) => {
  console.log('Mostrando detalles para el personaje:', character.id);
  selctedcharacter.value = character;
};

const closeModal = () => {
  selctedcharacter.value = null;
};


loadCharacters();
</script>
  
  