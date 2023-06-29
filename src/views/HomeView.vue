<template>
  <div class="container-md wrapper">
    <div class="cover">
      <div class="header">
        <h1 class="text-uppercase fw-bold">
          <svg xmlns="http://www.w3.org/2000/svg" width="30" height="30" viewBox="0 0 24 24"><g fill="lightsalmon"><path fill-rule="evenodd" d="M6.271 2.112c-.81.106-1.238.301-1.544.6c-.305.3-.504.72-.613 1.513C4.002 5.042 4 6.124 4 7.675v8.57a4.172 4.172 0 0 1 1.299-.593c.528-.139 1.144-.139 2.047-.138H20V7.676c0-1.552-.002-2.634-.114-3.451c-.109-.793-.308-1.213-.613-1.513c-.306-.299-.734-.494-1.544-.6c-.834-.11-1.938-.112-3.522-.112H9.793c-1.584 0-2.688.002-3.522.112Zm.488 4.483c0-.448.37-.811.827-.811h8.828a.82.82 0 0 1 .827.81a.82.82 0 0 1-.827.811H7.586a.82.82 0 0 1-.827-.81Zm.827 2.973a.82.82 0 0 0-.827.81c0 .448.37.811.827.811h5.517a.82.82 0 0 0 .828-.81a.82.82 0 0 0-.828-.811H7.586Z" clip-rule="evenodd"/><path d="M7.473 17.135H20c-.003 1.13-.021 1.974-.113 2.64c-.109.793-.308 1.213-.613 1.513c-.306.299-.734.494-1.544.6c-.834.11-1.938.112-3.522.112H9.793c-1.584 0-2.688-.002-3.522-.111c-.81-.107-1.238-.302-1.544-.601c-.305-.3-.504-.72-.613-1.513c-.041-.3-.068-.637-.084-1.02a2.464 2.464 0 0 1 1.697-1.537c.29-.076.667-.083 1.746-.083Z"/></g></svg>          English Dictionary
        </h1>
        <div class="search">
          <input type="text" class="input-text" placeholder="Enter word here..." v-model="word" @keyup.enter="getData">
          <button class="btn1" @click="getData"> <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 24 24"><path fill="currentColor" d="M9.5 3A6.5 6.5 0 0 1 16 9.5c0 1.61-.59 3.09-1.56 4.23l.27.27h.79l5 5l-1.5 1.5l-5-5v-.79l-.27-.27A6.516 6.516 0 0 1 9.5 16A6.5 6.5 0 0 1 3 9.5A6.5 6.5 0 0 1 9.5 3m0 2C7 5 5 7 5 9.5S7 14 9.5 14S14 12 14 9.5S12 5 9.5 5Z"/></svg> search</button>
        </div>
        <p v-if="!validQuery" class="error">Error in getting some data.</p>
        <div class="dictionary container" v-if="dataIsHere">
          <div class="d-flex justify-content-between w-80">
            <h2>{{ word }}</h2>
            <button class="sound" @click="getAudio">
              <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" viewBox="0 0 512 512"><path fill="currentColor" d="M426.7 256c0-71-43.4-131.8-105-157.5l-16.4 39.4C351.5 157.2 384 202.8 384 256c0 53.3-32.5 98.8-78.8 118.1l16.4 39.4C383.3 387.8 426.7 327 426.7 256zm-85.4 0c0-35.5-21.7-65.9-52.5-78.7l-16.4 39.4c15.4 6.4 26.2 21.6 26.2 39.4c0 17.7-10.8 32.9-26.2 39.4l16.4 39.4c30.8-13 52.5-43.4 52.5-78.9zm13.2-236.3L338 59.1C415.1 91.2 469.3 167.2 469.3 256c0 88.7-54.2 164.8-131.3 196.9l16.4 39.4C447 453.7 512 362.5 512 256S447 58.3 354.5 19.7zM0 149.3v213.3h85.3L234.7 512V0L85.3 149.3H0z"/></svg>
            </button>
          </div>
          <p class="error" v-if="sound">No Sound</p>
          <p ref="pos" class="pos"></p>
          <p ref="phonetic"></p>
          <div v-for="definition in definitionsArray" :key="definition">
            <p class="pt-1">{{ definition }}</p>
          </div>
      </div>  
      <div class="mx-auto pb-1">
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
    const dataIsHere = ref(false)
    const word = ref("")
    const definitionsArray = ref([])
    const phonetic = ref(null)
    const pos = ref(null)

    const getData = async () => {
      await getDefinition(word.value)
    }
    const getDefinition = async (word) => {
      definitionsArray.value.splice(0, definitionsArray.value.length)
      try {
        const url = " https://api.dictionaryapi.dev/api/v2/entries/en/ " + word
        dataIsHere.value = true
        const {data} = await axios.get(url)
        phonetic.value.textContent = data[0].phonetics[1].text
        pos.value.textContent = data[0].meanings[0].partOfSpeech
        console.log(data)
        data.map(({meanings}) => {
          meanings.map(({definitions}) => {

            definitions.forEach(({definition}) => {
              if (typeof definition === "string") {
                definitionsArray.value.push(definition)
              }
            })
          })
        })
        validQuery.value = true
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
      } catch(err) {
          sound.value = true
      }
    }

    const changeTheme = () => {
      const element = document.body
      element.classList.toggle("lightmode")
    }
    return {dataIsHere, definitionsArray, pos, changeTheme, validQuery, word, getDefinition, getData, phonetic, getAudio, sound}
  }
}
</script>

<style>
@import url("../assets/global.css");
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@500&display=swap');
</style>
