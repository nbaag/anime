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

  const increaseEp = (anime) => {
    anime.watched++
    console.log(anime.watched)
    localStorage.setItem('animeList', JSON.stringify(animeList.value))
  }

  const decreaseEp = (anime) => {
    anime.watched--
    localStorage.setItem('animeList', JSON.stringify(animeList.value))
  }

  onMounted(() => {
    animeList.value = JSON.parse(localStorage.getItem('animeList')) || []
  })
</script>

<template>
  <main>
    <form @submit.prevent="searchAnime">
      <input type="text" placeholder="Search anime..." v-model="query">
      <button type="submit">Search</button>
    </form>

    <div class="searched" v-if="animeSearchList.length > 0">
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
        <div class="anime" v-for="anime in animeList" :key="anime.id">
          <img :src="anime.image" />
          <h3>{{ anime.title }}</h3>
          <p class="episodes">{{ anime.watched }} / {{ anime.episodes }}</p>
          <button class="btn" @click="increaseEp(anime)">+</button>
          <button class="btn" @click="decreaseEp(anime)">-</button>
        </div>
      </div>
  </main>
</template>

<style scoped>

</style>
