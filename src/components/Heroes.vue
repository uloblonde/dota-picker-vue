<script lang="ts">
import { defineComponent } from "vue";
import heroess from "../assets/heroes.json";

// Declare Data Types
export interface Hero {
  id: number;
  name: string;
  name_loc: string;
  name_english_loc: string;
  primary_attr: number;
  complexity: number;
  image: string;
  attribute_img: string;
  
}

// Define Component
export default defineComponent({
  name: "Heroes",
  data() {
    return {
      heroes: [] as Hero[],
      searchQuery: "",
      currentPage: 1,
      heroesPerPage: 8,
      isLoading: true,
      id: 1,
    };
  },
  // Fetch Data
  async created() {
    try {
      const data = heroess;

      this.heroes = data.heroes.map((hero: any) => ({
        id: hero.id,
        name: hero.name,
        name_loc: hero.localized_name,
        name_english_loc: hero.name,
        primary_attr: 0,
        complexity: 0,
        image: hero.url_full_portrait,
        attribute_img: "",
      }));
      console.log(data);
      this.isLoading = false;
    } catch (error) {
      console.error(error);
      this.isLoading = false;
    }
  },
  // Pagination
  computed: {
    paginatedHeroes() {
      const startIndex = (this.currentPage - 1) * this.heroesPerPage;
      return this.filterHeroes().slice(startIndex, startIndex + this.heroesPerPage);
    },
  },
  // Search
  methods: {
    filterHeroes() {
      return this.heroes.filter((hero) => {
        const name = hero.name.toLowerCase();
        const nameLoc = hero.name_loc.toLowerCase();
        const searchQuery = this.searchQuery.toLowerCase();
        return name.includes(searchQuery) || nameLoc.includes(searchQuery);
      });
    },
    paginate(page: any) {
      this.currentPage = page;
    },
  },
});
</script>

<template>
  <div>
    <h1>Daftar Pahlawan Pertahanan Orang Masa Lampau 2</h1>
    <input v-model="searchQuery" placeholder="Search by name" class="w-1/4 mt-2 p-2 border rounded-lg" />

    <ul v-if="!isLoading">
      <div class="grid grid-cols-4 gap-4 my-4">
        <li v-for="hero in paginatedHeroes" :key="hero.id">
          <div>
            <img :src="hero.image" :alt="hero.name_loc" class="w-64 h-50 align-center mx-auto" />
            <h2>{{ hero.name_loc }}</h2>
          </div>
        </li>
      </div>
    </ul>

    <div v-else>Loading...</div>

    <div class="grid grid-rows-1 grid-flow-col gap-4">
      <button @click="paginate(1)" :disabled="currentPage === 1">First</button>
      <button @click="paginate(currentPage - 1)" :disabled="currentPage === 1">Previous</button>
      <span>{{ currentPage }} of {{ Math.ceil(filterHeroes().length / heroesPerPage) }}</span>
      <button @click="paginate(currentPage + 1)" :disabled="currentPage === Math.ceil(filterHeroes().length / heroesPerPage)">Next</button>
      <button @click="paginate(Math.ceil(filterHeroes().length / heroesPerPage))" :disabled="currentPage === Math.ceil(filterHeroes().length / heroesPerPage)">Last</button>
    </div>
  </div>
</template>
