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
    <div class="container">
      <form class="searchForm" @submit.prevent="searchAnime">
      <input type="text" placeholder="Search anime..." v-model="query">
      <button type="submit" class="btn">Search</button>
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
        <div class="anime" v-for="anime in sortedAnimeList" :key="anime.id">
          <img :src="anime.image" />
          <h3>{{ anime.title }}</h3>
          <div class="flex-1"></div>
          <p class="episodes">{{ anime.watched }} / {{ anime.episodes }}</p>
          <button class="btn" @click="increaseEp(anime)">+</button>
          <button class="btn" @click="decreaseEp(anime)">-</button>
        </div>
      </div>
    </div> 
  </main>
</template>

<style scoped>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}
.flex-1 {
  display: block;
  flex: 1 1 0%;
}
.container{
  max-width: 1100px;
  margin: 0 auto;
}

.searchForm{
  display: flex;
  max-width: 400px;
  margin: auto;
}

.searchForm input {
  border: none;
  background-color: rgb(214, 214, 214);
  padding-left: 4px;
  padding-right: 4px;
  width: 500px;
  height: 30px;
  margin: auto;
}

.searchForm input:focus {
  outline: none;
  background-color: rgb(201, 201, 201);

}
.searched{
  border: 1px solid #000;
  max-height: 800px;
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
}
.result img {
  height: 100px;
  width: 90px;
  object-fit: cover;
  border-radius: 10px;
}
.about{

}
.anime__title{

}
.anime__episodes{

}
.anime__synopsis{

}
.btn{
  border: none;
  display: block;
  cursor: pointer;
  padding: 5px 10px;
  font-weight: bold;
  text-transform: uppercase;
  margin-left: 6px;
}
.animeList{

}

.anime{
  display: flex;
  align-items: center;
  margin-top: 20px;
  background-color: rgb(214, 214, 214);
  padding: 5px;
  border-radius: 10px;
}

.anime img {
  height: 100px;
  width: 90px;
  object-fit: cover;
  border-radius: 10px;
  
}

.episodes{

}

</style>
