<script lang="ts">
import { defineComponent } from "vue";

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

export default defineComponent({
  name: "Heroes",
  data() {
    return {
      heroes: [] as Hero[],
      searchQuery: "",
      currentPage: 1,
      heroesPerPage: 10,
    };
  },
  async created() {
    try {
      const response = await fetch("https://dota2-heroes.p.rapidapi.com/heroes/english", {
        method: "GET",
        headers: {
          "X-RapidAPI-Key": "80c2bbaeebmshced4f2f640a3757p10570bjsnb5826b580528",
          "X-RapidAPI-Host": "dota2-heroes.p.rapidapi.com",
        },
      });
      this.heroes = await response.json();
    } catch (error) {
      console.error(error);
    }
  },
  computed: {
    paginatedHeroes() {
      const startIndex = (this.currentPage - 1) * this.heroesPerPage;
      return this.filterHeroes().slice(startIndex, startIndex + this.heroesPerPage);
    },
  },
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
    <h1>Dota 2 Heroes</h1>
    <input v-model="searchQuery" placeholder="Search by name or name_loc" />
    <ul>
      <div class="grid grid-cols-4 gap-4">
        <li v-for="hero in paginatedHeroes" :key="hero.id">
          <div>
            <img :src="hero.image" :alt="hero.name_loc" class="w-1/2 h-1/2 align-center mx-auto" />
            <h2>{{ hero.name_loc }}</h2>
          </div>
        </li>
      </div>
    </ul>
    <div>
      <button @click="paginate(1)" :disabled="currentPage === 1">First</button>
      <button @click="paginate(currentPage - 1)" :disabled="currentPage === 1">Previous</button>
      <span>{{ currentPage }} of {{ Math.ceil(filterHeroes().length / heroesPerPage) }}</span>
      <button @click="paginate(currentPage + 1)" :disabled="currentPage === Math.ceil(filterHeroes().length / heroesPerPage)">Next</button>
      <button @click="paginate(Math.ceil(filterHeroes().length / heroesPerPage))" :disabled="currentPage === Math.ceil(filterHeroes().length / heroesPerPage)">Last</button>
    </div>
  </div>
</template>
