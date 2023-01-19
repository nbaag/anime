<script setup>
import { ref, computed, onMounted } from "vue";
import AnimeItem from "./components/AnimeItem.vue";

const query = ref("");
const animeList = ref([]);
const animeSearchList = ref([]);

const sortedAnimeList = computed(() => {
  return animeList.value.sort((a, b) => {
    return a.title.localeCompare(b.title);
  });
});

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`;
  fetch(url)
    .then((res) => res.json())
    .then((data) => {
      animeSearchList.value = data.data;
    });
};

const addAnime = (anime) => {
  animeSearchList.value = [];
  query.value = "";

  animeList.value.push({
    id: anime.mal_id,
    title: anime.title,
    episodes: anime.episodes,
    image: anime.images.jpg.image_url,
    watched: 0,
  });

  localStorage.setItem("animeList", JSON.stringify(animeList.value));
};

const deleteAnime = (anime) => {
  animeList.value = animeList.value.filter((i) => i !== anime);
  localStorage.setItem("animeList", JSON.stringify(animeList.value));
};

onMounted(() => {
  animeList.value = JSON.parse(localStorage.getItem("animeList")) || [];
});
</script>

<template>
  <main>
    <div class="container">
      <form class="searchForm" @submit.prevent="searchAnime">
        <input type="text" placeholder="Search anime..." v-model="query" />
        <button type="submit" class="btn">Search</button>
      </form>

      <div class="searched" v-if="animeSearchList.length > 0 && query !== ''">
        <div class="result" v-for="anime in animeSearchList" :key="anime.mal_id">
          <img :src="anime.images.jpg.image_url" />

          <div class="about">
            <h1 class="anime__title">{{ anime.title }}</h1>
            <p class="anime__episodes">Episodes: {{ anime.episodes }}</p>
            <!-- <p class="anime__synopsis" v-if="anime.synopsis">
              {{ anime.synopsis.slice(0, 100) }}...
            </p> -->
            <button @click="addAnime(anime)" class="btn">Add</button>
          </div>
        </div>
      </div>

      <div class="animeList" v-if="animeList.length > 0">
        <div class="anime" v-for="anime in sortedAnimeList" :key="anime.id">
          <AnimeItem :anime="anime" :animeList="animeList" @delete="deleteAnime" />
        </div>
      </div>
    </div>
  </main>
</template>

<style lang="scss">
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  padding: 10px;
  background-color: rgb(108, 245, 238);
  font-family: "Fredoka One";
}

.container {
  max-width: 1100px;
  margin: 0 auto;
}

.searchForm {
  display: flex;
  max-width: 400px;
  margin: auto;

  input {
    border: none;
    background-color: rgb(238, 169, 215);
    padding-left: 4px;
    padding-right: 4px;
    width: 500px;
    height: 30px;
    margin: auto;

    &:focus {
      outline: none;
      background-color: rgb(252, 146, 211);
    }
  }
}

.searched {
  margin: 0 auto;
  border: 1px solid #000;
  border-radius: 10px;
  max-height: 640px;
  max-width: 900px;
  overflow-y: scroll;
  padding: 5px;
  background-color: rgb(201, 201, 201);
  scrollbar-width: none;
}

.searched::-webkit-scrollbar {
  display: none;
}

.result {
  display: flex;
  margin-top: 10px;
  border: 1px solid #000;
  border-radius: 10px;
  padding: 3px;
  background-color: #fff;

  img {
    margin-right: 10px;
    height: 100px;
    width: 90px;
    object-fit: cover;
    border-radius: 10px;
  }
}

.btn {
  background: none;
  border: none;
  display: inline-block;
  cursor: pointer;
  padding: 5px 10px;
  font-weight: bold;
  text-transform: uppercase;
  transition: 0.2s;
  font-family: "Fredoka One";

  &:hover {
    background-color: rgb(230, 127, 150);
  }
}

.animeList {
  h2 {
    font-size: 30px;
  }
}

.anime {
  box-shadow: 2px 2px 20px #000;
  position: relative;
  margin-top: 20px;
  background-color: rgb(238, 169, 215);
  padding: 5px;
  border-radius: 10px;
  min-height: 110px;
  max-width: 700px;
  margin: 20px auto;

  h3 {
    font-size: 25px;
  }

  img {
    height: 100px;
    width: 90px;
    float: left;
    object-fit: cover;
    border-radius: 10px;
    margin-right: 5px;
  }
}
</style>
