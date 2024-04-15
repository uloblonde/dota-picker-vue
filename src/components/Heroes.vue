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
});
</script>

<template>
  <div>
    <h1>Dota 2 Heroes</h1>
    <ul>
      <li v-for="hero in heroes" :key="hero.id">
        <img :src="hero.image" :alt="hero.name_loc" />
        <h2>{{ hero.name_loc }}</h2>
      </li>
    </ul>
  </div>
</template>


