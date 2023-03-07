<template>
    <div class="mainPageMobile">
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
  
  <style scoped>
  /* Default styles for all screen sizes */
* {
    box-sizing: border-box;
  }
  
  body {
    margin: 0;
    font-family: Arial, sans-serif;
  }
  
  h1 {
    font-size: 32px;
    margin-bottom: 24px;
  }
  
  .flex {
    display: flex;
    flex-direction: column;
    gap: 16px;
  }
  
  label {
    font-weight: bold;
  }
  
  input, button {
    font-size: 16px;
    padding: 8px;
    border-radius: 4px;
    border: 1px solid #ccc;
  }
  
  button {
    background: #007bff;
    color: #fff;
  }
  
  .oneGame {
    margin-top: 8px;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 4px;
  }
  
  .game-container {
    width: 100%;
  }
  
  .eachGame {
    border: 1px solid #ccc;
    border-radius: 4px;
    padding: 8px;
    background: #fff;
  }
  
  .all-game-details {
    display: flex;
    flex-wrap: wrap;
    gap: 8px;
  }
  
  .game-info {
    flex: 1 0 45%;
  }
  
  h2 {
    margin-bottom: 4px;
  }
  
  /* Media query for screens narrower than 600px */
  @media (max-width: 600px) {
  
    .mainPageMobile {
      padding: 16px;
    }
  
    h1 {
      font-size: 24px;
    }
  
    .flex {
      gap: 12px;
    }
  
    input, button {
      font-size: 14px;
      padding: 6px;
    }
  
    .game-info {
      flex: 1 0 100%;
    }
  
  }
  
  </style>