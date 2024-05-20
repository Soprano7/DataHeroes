<template>
  <div>
    <h1>Rick and Morty Characters</h1>

    <div class="filters">
      <input v-model="filters.name" type="text" placeholder="Filter by name" />
      <select v-model="filters.status">
        <option value="">All Statuses</option>
        <option value="alive">Alive</option>
        <option value="dead">Dead</option>
        <option value="unknown">Unknown</option>
      </select>
      <button @click="applyFilters">Apply</button>
    </div>

    <div class="characters-grid">
      <CharacterCard v-for="character in characters" :key="character.id" :character="character" />
    </div>

    <div class="pagination">
      <button @click="prevPage" :disabled="page === 1">Previous</button>
      <button @click="nextPage" :disabled="!hasNextPage">Next</button>
    </div>
  </div>
</template>

<script setup>
import { ref, reactive, onMounted } from 'vue';
import axios from 'axios';
import CharacterCard from './components/CharacterCard.vue';

const characters = ref([]);
const page = ref(1);
const filters = reactive({
  name: '',
  status: ''
});
const hasNextPage = ref(false);

const fetchCharacters = async () => {
  const { name, status } = filters;
  const response = await axios.get(`https://rickandmortyapi.com/api/character`, {
    params: { page: page.value, name, status }
  });
  characters.value = response.data.results;
  hasNextPage.value = response.data.info.next !== null;
};

const applyFilters = () => {
  page.value = 1;
  fetchCharacters();
};

const nextPage = () => {
  if (hasNextPage.value) {
    page.value++;
    fetchCharacters();
  }
};

const prevPage = () => {
  if (page.value > 1) {
    page.value--;
    fetchCharacters();
  }
};

onMounted(fetchCharacters);
</script>

<style scoped>
body {
  font-family: Arial, sans-serif;
  background-color: #f9f9f9;
  color: #333;
  margin: 0;
  padding: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-direction: column;
}

h1 {
  margin-top: 20px;
}

.filters {
  margin: 20px 0;
  display: flex;
  gap: 10px;
}

.characters-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(200px, 1fr));
  gap: 20px;
  width: 100%;
  padding: 20px;
  box-sizing: border-box;
}

.pagination {
  margin: 20px 0;
  display: flex;
  gap: 10px;
}
</style>
