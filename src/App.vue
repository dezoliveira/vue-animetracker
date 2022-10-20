<script setup>
import {  ref, computed, onMounted } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) =>{
    return a.title.localCompare(b.title)
  })
})

const searchAnime = () => {
  const url = `https://api.jikan.moe/v4/anime?q=${query.value}`
  fetch(url)
    .then(res => res.json())
    .then(res => {
      search_results.value = res.data
    })
}

const handleInput = e => {
  if(!e.target.value){
    search_results.value = []
  }
}

const addAnime = anime => {
  search_results.value = []
  query.value = ''

  my_anime.value.push({
    id: anime.mail_id,
    title: anime.title,
    image: anime.images.jpg,
    total_episodes: anime.episodes, 
    watched_episodes: 0
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = anime => {
  anime.watched_episodes ++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = anime => {
  anime.watched_episodes --
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() =>{
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})

</script>

<template>

  <div class="columns">
    <div class="column">
      teste
    </div>
    <div class="column">
      <input class="input is-primary" type="text" placeholder="Primary input">
    </div>
    <div class="column">
      teste
    </div>
  </div>

</template>

<style scoped>

</style>
