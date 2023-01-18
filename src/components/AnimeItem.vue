<template>
  <img :src="props.anime.image" />

  <div class="title">
    <h3>{{ props.anime.title }}</h3>
    <button class="btn" @click="$emit('delete', props.anime)">delete</button>
  </div>

  <div class="ep">
    <p class="episodes">{{ props.anime.watched }} / {{ props.anime.episodes }}</p>
    <button class="btn" @click="increaseEp(props.anime)">+</button>
    <button class="btn" @click="decreaseEp(props.anime)">-</button>
  </div>
</template>

<script setup>
const props = defineProps(["anime", "animeList"]);
const emits = defineEmits(["delete"]);

const increaseEp = (anime) => {
  if (anime.watched < anime.episodes) {
    anime.watched++;
    localStorage.setItem("animeList", JSON.stringify(props.animeList));
  } else if (anime.watched >= anime.episodes) {
    alert("max");
  }
};

const decreaseEp = (anime) => {
  if (anime.watched > 0) {
    anime.watched--;
    localStorage.setItem("animeList", JSON.stringify(props.animeList));
  } else if (anime.watched <= 0) {
    alert("min");
  }
};
</script>

<style></style>
