<script setup>
  import {ref, computed, onMounted} from 'vue'

  const query = ref('')
  const animeList = ref([])
  const animeSearchList = ref([])

  const sortedAnimeList = computed(() => {
    return animeList.value.sort((a, b) => {
      return a.title.localeCompare(b.title)
    })
  })

  const searchAnime = () => {
    const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
    fetch(url)
      .then(res => res.json())
      .then(data => {
        animeSearchList.value = data.data
      })
  }

  const addAnime = (anime) => {
    animeSearchList.value = []
    query.value = ''

    animeList.value.push({
      id: anime.mal_id,
      title: anime.title,
      episodes: anime.episodes,
      image: anime.images.jpg.image_url,
      watched: 0
    })

    localStorage.setItem('animeList', JSON.stringify(animeList.value))
  }

  const deleteAnime = (anime) => {
    animeList.value = animeList.value.filter((i) => i !== anime)
    localStorage.setItem('animeList', JSON.stringify(animeList.value))
  }

  const increaseEp = (anime) => {
    if (anime.watched < anime.episodes) {
      anime.watched++
      localStorage.setItem('animeList', JSON.stringify(animeList.value))
    } else if (anime.watched >= anime.episodes) {
      alert('max')
    } 
  }

  const decreaseEp = (anime) => {
    if (anime.watched > 0) {
      anime.watched--
      localStorage.setItem('animeList', JSON.stringify(animeList.value))
    } else if (anime.watched <= 0) {
      alert('min')
    } 
  }

  onMounted(() => {
    animeList.value = JSON.parse(localStorage.getItem('animeList')) || []
  })
</script>

<template>
  <main>
    <div class="container">
      <form class="searchForm" @submit.prevent="searchAnime">
      <input type="text" placeholder="Search anime..." v-model="query">
      <button type="submit" class="btn">Search</button>
    </form>

    <div class="searched" v-if="animeSearchList.length > 0 && query !== ''">
      <div class="result" v-for="anime in animeSearchList" :key="anime.mal_id">
        <img :src="anime.images.jpg.image_url" />

        <div class="about">
          <h1 class="anime__title">{{ anime.title }}</h1>
          <p class="anime__episodes">{{ anime.episodes }}</p>
          <p class="anime__synopsis" v-if="anime.synopsis"> {{ anime.synopsis.slice(0, 100) }}...</p>
          <button @click="addAnime(anime)" class="btn">Add</button>
        </div>
      </div>
    </div>

    <div class="animeList" v-if="animeList.length > 0">
        <h2>Anime</h2>
        <div class="anime" v-for="anime in sortedAnimeList" :key="anime.id">
          <img :src="anime.image" />

          <div class="title">
            <h3>{{ anime.title }}</h3>
            <button class="btn" @click="deleteAnime(anime)">delete</button>
          </div>

          <div class="ep">
            <p class="episodes">{{ anime.watched }} / {{ anime.episodes }}</p>
            <button class="btn" @click="increaseEp(anime)">+</button>
            <button class="btn" @click="decreaseEp(anime)">-</button>
          </div>     
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
  font-family: 'Fredoka One';
}

.container{
  max-width: 1100px;
  margin: 0 auto;
}

.searchForm{
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

.searched{
  border: 1px solid #000;
  max-height: 640px;
  overflow-y: scroll;
  padding: 5px;
  background-color: rgb(201, 201, 201);
}

.result{
  display: flex;
  margin-top: 10px;
  border: 1px solid #000;
  border-radius: 10px;
  padding: 3px;
  background-color: #fff;

  img {
    height: 100px;
    width: 90px;
    object-fit: cover;
    border-radius: 10px;
  }
}

.btn{
  background: none;
  border: none;
  display: inline-block;
  cursor: pointer;
  padding: 5px 10px;
  font-weight: bold;
  text-transform: uppercase;
  transition: 0.5s;
  font-family: 'Fredoka One';

  &:hover {
    background-color: rgb(230, 127, 150);
  }
}

.animeList {

  h2{
    font-size: 30px;
  }
} 

.anime{
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

.title {
  display: flex;
  justify-content: space-between;
}
.episodes {
  margin: auto;
  font-size: 20px;
}

.ep {
  position: absolute;
  display: flex;
  left: 100px;
  bottom: 10px;
  .btn {
    font-size: 20px;
  }
}

</style>
