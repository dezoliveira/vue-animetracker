<script setup>
import { ref, computed, onMounted } from 'vue'

const query = ref('')
const my_anime = ref([])
const search_results = ref([])

const my_anime_asc = computed(() => {
  return my_anime.value.sort((a, b) =>{
    return a.title.localeCompare(b.title)
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

const handleInput = (e) => {
  if(!e.target.value){
    search_results.value = []
  }
}

const addAnime = anime => {
  search_results.value = []
  query.value = ''

  my_anime.value.push({
    id: anime.mal_id,
    title: anime.title,
    image: anime.images.jpg.image_url,
    total_episodes: anime.episodes, 
    watched_episodes: 0
  })

  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const increaseWatch = (anime) => {
  anime.watched_episodes ++
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

const decreaseWatch = (anime) => {
  anime.watched_episodes --
  localStorage.setItem('my_anime', JSON.stringify(my_anime.value))
}

onMounted(() =>{
  my_anime.value = JSON.parse(localStorage.getItem('my_anime')) || []
})

</script>

<template>
  <link rel="stylesheet" 
    href="https://use.fontawesome.com/releases/v5.2.0/css/all.css" 
    integrity="sha384-hWVjflwFxL6sNzntih27bfxkr27PmbbK/iSvJ+a4+0owXq79v+lsFkW54bOGbiDQ" 
    crossorigin="anonymous"
  >
  <img class="background" src="/background.jpg" />
  <main class="column is-half is-offset-one-quarter">
    <div class="column is-half is-offset-one-third">
      <h1 class="title">Anime tracker</h1>
    </div>
    <section class="columns is-half is-offset-one-third p-4">
      <div class="column">
        <form class="flex" @submit.prevent="searchAnime">
          <input 
            class="input is-primary" 
            type="text" 
            placeholder="Pesquisar Anime"
            v-model="query"
            @input="handleInput"
          />
            <button type="submit" class="search button is-primary" style="">Pesquisar</button>
        </form>
      </div>

    </section>
    <section class="flex-column columns box is-centered scrool p-4" v-if="search_results.length > 0">
      <div class="box anime-box" v-for="anime in search_results.slice(0,10)">
        <img class="anime-img" :src="anime.images.jpg.image_url" />
        <div>
          <h3>{{anime.title}}</h3>
          <p :title="anime.synopsis" v-if="anime.synopsis">
            {{  anime.synopsis.slice(0, 100)}}...
          </p>
          <button 
            class="add-button button is-primary" 
            @click="addAnime(anime)">
              <span>
                <i class="fas fa-plus">

                </i>
              </span>
          </button>
        </div>
      </div>
    </section>
    <section class="flex-column columns p-4">
      <div v-if="my_anime.length > 0">
        <h2>My Anime</h2>

        <div v-for="anime in my_anime_asc" class="box my-anime-box">
          <img class="my-anime-img" :src="anime.image"/>
          <h3>{{anime.title}}</h3>
          <div class="flex is-justify-content-space-between">
            <span>
              {{anime.watched_episodes}} / {{anime.total_episodes}}
            </span>
            <div>
              <button
              class="add-button button is-primary m-1"
              v-if="anime.total_episodes !== anime.whatched_episodes"
              @click="increaseWatch(anime)"
              >
                <span>
                  <i class="fas fa-plus">

                  </i>
                </span>
              </button>
              <button
                class="add-button button is-danger m-1"
                v-if="anime.total_episodes > 0"
                @click="decreaseWatch(anime)"
              > 
                <span>
                  <i class="fas fa-minus">

                  </i>
                </span>
              </button>
            </div>
          </div>
        </div>
      </div>
    </section>
  </main>
  <footer class="waves">
    <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 1440 320"><path fill="#00cba9" fill-opacity="1" d="M0,288L48,272C96,256,192,224,288,197.3C384,171,480,149,576,165.3C672,181,768,235,864,250.7C960,267,1056,245,1152,250.7C1248,256,1344,288,1392,304L1440,320L1440,320L1392,320C1344,320,1248,320,1152,320C1056,320,960,320,864,320C768,320,672,320,576,320C480,320,384,320,288,320C192,320,96,320,48,320L0,320Z"></path></svg>
  </footer>
</template>

<style scoped>
  @import url('https://fonts.googleapis.com/css2?family=Roboto:wght@400;500&display=swap');

  *{

  }

  body{
    font-family: 'Roboto', sans-serif;
    background-image: url("../public/background.jpg");
  }

  .background {
    width: 100%;
    height: 100vh;
    position: absolute;
    z-index: -100;
  }

  .title{
    font-size: 32px;
  }

  .flex{
    display: flex;
    justify-content: space-around;
  }

  .flex-column{
    display: flex;
    flex-direction: column;
  }

  .flex-column img{
    width: 64px;
  }

  .search{
    margin: 0 0 0 25px;
  }

  .my-anime-box{
    height: 200px;
  }

  .my-anime-img{
    border-radius: 5px;
    height: 80px
  }

  .scrool{
    width: 100%;
    height: 80vh;
    overflow-y: auto;
    padding: 8px;
  }
  
  .scrool::-webkit-scrollbar{
    width: 8px;
  }
  
  .scrool::-webkit-scrollbar-track{
    background: #f1f1f1;
    border-radius: 25px;
  }
  
  .scrool::-webkit-scrollbar-thumb{
    background: #1ab394;
    border-radius: 25px;
  }
  
  .scrool .dropdown-menu .dropdown-item a:hover{
    background: #1ab394;
    color: #fff;
  }

  .add-button{
    width: 40px;
    border-radius: 50%;
    margin: 10px 0 0 0;
  }

  svg{
    position: absolute;
    bottom: 0;
    z-index: -1;
  }

  ::-webkit-scrollbar{
    width: 8px;
  }
  
  ::-webkit-scrollbar-track{
    background: #f1f1f1;
    border-radius: 25px;
  }
  
  ::-webkit-scrollbar-thumb{
    background: #1ab394;
    border-radius: 25px;
  }
</style>
