<template>
  <div>
    <div>
      <input v-model="name" placeholder="Search by name" />
      <select v-model="status">
        <option value="">All</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters">Применить</button>
    </div>
    
    <div v-if="characters.length">
      <div v-for="character in characters" :key="character.id" class="character-card">
        <img :src="character.image" :alt="character.name" />
        <p>{{ character.name }}</p>
        <p>{{ character.status }}</p>
      </div>
    </div>
    <div v-else>
      <p>No characters found</p>
    </div>
    <button @click="prevPage" :disabled="page === 1">Previous</button>
    <button @click="nextPage">Next</button>
  </div>
</template>

<script>
import api from '../services/api';

export default {
  data() {
    return {
      characters: [],
      page: 1,
      name: '',
      status: '',
      filters: {
        name: '',
        status: ''
      }
    };
  },
  created() {
    this.fetchCharacters();
  },
  methods: {
    fetchCharacters() {
      const { name, status } = this.filters;
      api.getCharacters(this.page, name, status).then(response => {
        this.characters = response.data.results;
      });
    },
    applyFilters() {
      this.filters.name = this.name;
      this.filters.status = this.status;
      this.page = 1;  // Сброс страницы при применении фильтров
      this.fetchCharacters();
    },
    nextPage() {
      this.page++;
      this.fetchCharacters();
    },
    prevPage() {
      if (this.page > 1) {
        this.page--;
        this.fetchCharacters();
      }
    }
  }
};
</script>

<style scoped>
.character-card {
  display: inline-block;
  margin: 10px;
  text-align: center;
}
.character-card img {
  width: 100px;
  height: 100px;
  border-radius: 50%;
}
</style>
