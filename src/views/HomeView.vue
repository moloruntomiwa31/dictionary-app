<template>
  <div class="container-md wrapper">
    <div class="cover">
      <div class="header">
        <h1 class="text-uppercase fw-bold">English Dictionary</h1>
        <div class="search">
          <input type="text" class="input-text" placeholder="Enter word here..." v-model="word">
          <button class="btn1" @click="getDefinition"> <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24"><path fill="currentColor" d="M9.5 3A6.5 6.5 0 0 1 16 9.5c0 1.61-.59 3.09-1.56 4.23l.27.27h.79l5 5l-1.5 1.5l-5-5v-.79l-.27-.27A6.516 6.516 0 0 1 9.5 16A6.5 6.5 0 0 1 3 9.5A6.5 6.5 0 0 1 9.5 3m0 2C7 5 5 7 5 9.5S7 14 9.5 14S14 12 14 9.5S12 5 9.5 5Z"/></svg> search</button>
        </div>
        <p v-if="!validQuery" class="error">Error in getting some data.</p>
        <div class="dictionary container">
          <div class="d-flex justify-content-between w-80">
            <h2>{{ word }}</h2>
            <button class="sound" @click="getAudio">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 512 512"><path fill="currentColor" d="M426.7 256c0-71-43.4-131.8-105-157.5l-16.4 39.4C351.5 157.2 384 202.8 384 256c0 53.3-32.5 98.8-78.8 118.1l16.4 39.4C383.3 387.8 426.7 327 426.7 256zm-85.4 0c0-35.5-21.7-65.9-52.5-78.7l-16.4 39.4c15.4 6.4 26.2 21.6 26.2 39.4c0 17.7-10.8 32.9-26.2 39.4l16.4 39.4c30.8-13 52.5-43.4 52.5-78.9zm13.2-236.3L338 59.1C415.1 91.2 469.3 167.2 469.3 256c0 88.7-54.2 164.8-131.3 196.9l16.4 39.4C447 453.7 512 362.5 512 256S447 58.3 354.5 19.7zM0 149.3v213.3h85.3L234.7 512V0L85.3 149.3H0z"/></svg>
            </button>
          </div>
          <p class="error" v-if="sound">No Sound</p>
          <p ref="pos" class="pos"></p>
          <p ref="phonetic"></p>
          <p ref="definition"></p>
      </div>  
      <div class="mx-auto">
        <button @click="changeTheme" class="theme mt-3 p-2 border-0 rounded">
          <svg xmlns="http://www.w3.org/2000/svg" width="24" height="24" viewBox="0 0 20 20"><path fill="currentColor" d="M10 3.5a6.5 6.5 0 1 1 0 13v-13ZM10 2a8 8 0 1 0 0 16a8 8 0 0 0 0-16Z"/></svg>
        </button>
      </div>
      </div>
    </div> 
  </div>
</template>

<script>
import { ref } from "vue";
import axios from "axios"
export default {

  name: 'HomeView',
  setup() {
    let validQuery = ref(true)
    let sound = ref(false)
    const word = ref("")
    const definition = ref(null)
    const phonetic = ref(null)
    const pos = ref(null)

    const getDefinition = async () => {
      try {
        const url = " https://api.dictionaryapi.dev/api/v2/entries/en/ " + word.value
        const {data} = await axios.get(url)
        phonetic.value.textContent = data[0].phonetics[1].text
        pos.value.textContent = data[0].meanings[0].partOfSpeech
        definition.value.textContent = data[0].meanings[0].definitions[0].definition
        console.log(data[0]);
        sound.value = false
      } catch (err) {
        validQuery.value = false
      }   
    }

    const getAudio = async () => {
      try {
        const url = " https://api.dictionaryapi.dev/api/v2/entries/en/ " + word.value
        const {data} = await axios.get(url)
        let audio = data[0].phonetics[1].audio
        new Audio(audio).play()
        validQuery.value = true
        console.log(data[0]);
      } catch(err) {
          sound.value = true
      }
    }

    const changeTheme = () => {
      const element = document.body
      element.classList.toggle("lightmode")
    }
    return {pos, changeTheme, validQuery, word, getDefinition, definition, phonetic, getAudio, sound}
  }
}
</script>

<style>
@import url("../assets/global.css");
</style>
