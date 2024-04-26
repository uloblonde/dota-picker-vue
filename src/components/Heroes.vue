<script lang="ts">
import { defineComponent } from "vue";
import heroes from "../assets/heroes.json";

interface Hero {
  id: number;
  name: string;
  name_loc: string;
  image: string;
}

export default defineComponent({
  data() {
    return {
      heroes: [] as Hero[],
      searchQuery: "",
      currentPage: 1,
      heroesPerPage: 8,
      isLoading: true,
    };
  },
  async created() {
    try {
      this.heroes = heroes.heroes.map((hero) => ({
        id: hero.id,
        name: hero.name,
        name_loc: hero.localized_name,
        image: hero.url_full_portrait,
      }));
      this.isLoading = false;
    } catch (error) {
      console.error(error);
      this.isLoading = false;
    }
  },
  computed: {
    paginatedHeroes() {
      const startIndex = (this.currentPage - 1) * this.heroesPerPage;
      return this.filteredHeroes.slice(startIndex, startIndex + this.heroesPerPage);
    },
    filteredHeroes() {
      return this.heroes.filter((hero) => {
        const name = hero.name.toLowerCase();
        const nameLoc = hero.name_loc.toLowerCase();
        const searchQuery = this.searchQuery.toLowerCase();
        return name.includes(searchQuery) || nameLoc.includes(searchQuery);
      });
    },
  },
  methods: {
    paginate(page: number) {
      this.currentPage = page;
    },
  },
});
</script>

<template>
  <div>
    <h1>Daftar Pahlawan Pertahanan Orang Masa Lampau 2</h1>
    <input v-model="searchQuery" placeholder="Search by name" />

    <ul v-if="!isLoading">
      <li v-for="hero in paginatedHeroes" :key="hero.id">
        <img :src="hero.image" :alt="hero.name_loc" />
        <h2>{{ hero.name_loc }}</h2>
      </li>
    </ul>

    <div v-else>Loading...</div>

    <div>
      <button @click="paginate(1)" :disabled="currentPage === 1">First</button>
      <button @click="paginate(currentPage - 1)" :disabled="currentPage === 1">Previous</button>
      <span>{{ currentPage }} of {{ Math.ceil(filteredHeroes.length / heroesPerPage) }}</span>
      <button @click="paginate(currentPage + 1)" :disabled="currentPage === Math.ceil(filteredHeroes.length / heroesPerPage)">Next</button>
      <button @click="paginate(Math.ceil(filteredHeroes.length / heroesPerPage))" :disabled="currentPage === Math.ceil(filteredHeroes.length / heroesPerPage)">Last</button>
    </div>
  </div>
</template>