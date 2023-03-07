<template>
  <div class="mainPage">
    <h1>Game CRUD</h1>

    <div v-if="!loading" :style="flex" class="main">
      <!-- Create Single Game -->
      <div id="create-game-form" class="flex">
        <label for="name">Name:</label>
        <input v-model="name" type="text" name="name" id="name">
        <label for="url">URL:</label>
        <input v-model="url" type="text" name="url" id="url">
        <label for="author">Author:</label>
        <input v-model="author" type="text" name="author" id="author">
        <label for="published-date">Published Date:</label>
        <input v-model="publishedDate" type="text" name="published-date" id="published-date">
        <button @click="createGame()" type="submit">Create</button>
      </div>

      <!-- Read Single Game -->
      <div class="readGame flex">
        <label for="read-game-id">Enter Game ID to read:</label>
        <input v-model="game.id" type="text" name="read-game-id" id="read-game-id">
        <button type="button" @click="readGame(game.id)">Read</button>
        <div v-if="game.name" class="oneGame">
          name : {{ game.name }}, url : {{ game.url }}, author : {{ game.author }}, publishedDate: {{ game.publishedDate
          }}
        </div>
        <div id="read-game-result"></div>
      </div>

      <!-- Get All Games -->
      <div :style="flex" style="flex-wrap: wrap; align-items: center">
        <button @click="getAllGames()">Get all games</button>
        <div v-for="(game, id) in games" :key="game.id" class="game-container">
          <div class="eachGame">
            <h2>{{ id + 1 }}. &nbsp;</h2>
            <div class="all-game-details">
              <div class="game-info">
                <h2>Name:</h2>
                <p>{{ game.name }}</p>
              </div>
              <div class="game-info">
                <h2>URL:</h2>
                <p>{{ game.url }}</p>
              </div>
              <div class="game-info">
                <h2>Author:</h2>
                <p>{{ game.author }}</p>
              </div>
              <div class="game-info">
                <h2>Published Date:</h2>
                <p>{{ game.publishedDate }}</p>
              </div>
              <div class="game-info">
                <h2> id:</h2>
                <p>{{ game.id }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Update Single Game -->
      <div id="update-game-form" class="flex">
        <label for="update-game-id">Enter Game ID to update:</label>
        <input v-model="updatedGame.id" type="text" name="update-game-id" id="update-game-id">
        <label for="update-name">Name:</label>
        <input v-model="updatedGame.name" type="text" name="update-name" id="update-name">
        <label for="update-url">URL:</label>
        <input v-model="updatedGame.url" type="text" name="update-url" id="update-url">
        <label for="update-author">Author:</label>
        <input v-model="updatedGame.author" type="text" name="update-author" id="update-author">
        <label for="update-published-date">Published Date:</label>
        <input v-model="updatedGame.publishedDate" type="text" name="update-published-date" id="update-published-date">
        <button @click="updateGame(updatedGame.id)" type="submit">Update</button>
      </div>

      <!-- Delete Single Game -->
      <div class="deleteSingleGame flex">
        <label for="delete-game-id">Enter Game ID to delete:</label>
        <input v-model="deleteId" type="text" name="delete-game-id" id="delete-game-id">
        <button type="button" @click="deleteGame(deleteId)">Delete</button>
        <div id="delete-game-result"></div>
      </div>
      <div>
        <button @click="deleteAll()" style="background: red">Delete All Games</button>
      </div>
    </div>
    <h1 v-if="loading">Loading...</h1>
  </div>
</template>

<script setup>
import axios from 'axios'
import { ref } from 'vue';
const name = ref('hi'), url = ref('hi'), author = ref('hi'), publishedDate = ref('hi');
let games = ref([])
let game = ref({ id: "", name: "", url: "", author: "", publishedDate: "" })
let updatedGame = ref({ id: "", name: "", url: "", author: "", publishedDate: "" })
const deleteId = ref('')
const baseUrl = import.meta.env.VITE_MAIN_URL

const loading = ref(false)

const flex = 'display: flex; flex-direction: column; '

const createGame = async () => {
  try {
    loading.value = true
    const response = await axios.post(baseUrl + 'games/create', {
      name: name.value,
      url: url.value,
      author: author.value,
      publishedDate: publishedDate.value
    });
    loading.value = false
    console.log(response.data);
    alert('Game created successfully!');
  } catch (error) {
    console.error(error);
    alert('Error creating game!');
  }
}

const getAllGames = async () => {
  try {
    loading.value = true
    const response = await axios.get(baseUrl + 'games/find/all');
    response.data.forEach(({
      url, name, author, publishedDate, _id
    }) => {
      games.value.push(
        {
          url, name, author, publishedDate, id: _id
        }
      )
    })
    loading.value = false

  } catch (error) {
    console.error(error);
    alert('Error getting all games!');
  }
};

const readGame = async (id) => {
  try {
    loading.value = true
    const response = await axios.get(baseUrl + 'games/' + id);
    const { name, url, publishedDate, author } = response.data
    game.value.name = name; game.value.url = url; game.value.publishedDate = publishedDate; game.value.author = author
    loading.value = false

  } catch (error) {
    console.error(error);
    alert('Error getting game!');
  }
}

const updateGame = async (id) => {
  try {
    loading.value = true
    const { name, url, author, publishedDate } = updatedGame.value
    const response = await axios.put(baseUrl + 'games/' + id, {
      name: name,
      url: url,
      author: author,
      publishedDate: publishedDate
    });
    loading.value = false

    alert('Game updated successfully!');
  } catch (error) {
    console.error(error);
    alert('Error updating game!');
  }
}

const deleteGame = async (id) => {
  try {
    loading.value = true
    const response = await axios.delete(baseUrl + 'games/' + id);
    loading.value = false

    alert('Game deleted successfully!');
  } catch (error) {
    alert('Error deleting game!');
  }
}

const deleteAll = async () => {
  try {
    loading.value = true
    const response = await axios.delete(baseUrl + 'games/deleteAll');
    loading.value = false

    alert('Games deleted successfully!');
  } catch (error) {
    alert('Error deleting game!');
  }
}


</script>

<style scoped> body {
   width: 100vw;
   height: 100vh;
 }

 /* main container */
 .mainPage {
   font-family: Arial, sans-serif;
   text-align: center;
   height: 100%;
   display: flex;
   flex-direction: column;
   align-items: center;
   width: 100%
 }

 .main {
   display: flex;
   height: 70%;
   justify-content: space-between;
   align-items: center;
 }

 .flex {
   display: flex;
   max-width: 100vw;
 }

 /* heading */
 h1 {
   margin-top: 50px;
   margin-bottom: 30px;
 }

 button {
   background-color: #4CAF50;
   color: white;
   padding: 10px 20px;
   border: none;
   border-radius: 5px;
   cursor: pointer;
   margin-top: 10px;
 }

 input {
   width: 5rem;
   border-radius: 0.5rem;
   padding: 0.7rem;
   margin: 0rem 0.5rem;
 }

 button:hover {
   background-color: #3e8e41;
 }

 .eachGame,
 .all-game-details {
   height: 100%;
   width: 80%;
   display: flex;
   align-items: start;
   flex-wrap: wrap;
   padding-left: 0.2rem;
 }

 .eachGame {
   border: 0.05rem solid black;
   border-radius: 0.5rem;
 }

 /* game details container */
 .game-container {
   display: flex;
   align-items: center;
   width: fit-content;
   margin: 0.6rem
 }

 /* game details */
 .all-game-details {
   display: flex;
 }

 /* game info */
 .game-info {
   display: flex;
   align-items: center;
   margin: 0rem 0.2rem
 }

 /* game info heading */
 .game-info h2 {
   margin-right: 10px;
 }

 /* game info text */
 .game-info p {
   margin: 0;
 }

 /* game number */
 .game-container h2 {
   margin-right: 10px;
   font-size: 1.5rem;
   font-weight: bold;
 }

 /* delete game input */
 .deleteSingleGame input {
   width: 50%;
   margin-right: 10px;
 }

 /* read game input */
 .readGame input {
   width: 50%;
   margin-right: 10px;
 }

 /* one game details */
 .oneGame {
   margin-top: 20px;
   font-size: 1.2rem;
   font-weight: bold;
 }

 /* error message */
 #error-message {
   color: red;
   font-size: 0.8rem;
   margin-top: 5px;
 }
</style>