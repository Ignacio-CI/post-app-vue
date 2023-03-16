<script setup>
import { ref, onMounted } from 'vue';
import BlogPost from './components/BlogPost.vue';
import PaginatePost from './components/PaginatePost.vue'
import LoadingSpinner from './components/loadingSpinner.vue'

const posts = ref([])
const postXPage = 10;
const inicio = ref(0);
const fin = ref(postXPage);
const loading = ref(true);

const favorito = ref('');

const cambiarFavorito = (title) => {
  favorito.value = title;
}

const next = () => {
  inicio.value += postXPage
  fin.value += postXPage
}

const prev = () => {
  inicio.value -= postXPage;
  fin.value -= postXPage;
}

onMounted(async() => {
  try{
    const res = await fetch('https://jsonplaceholder.typicode.com/posts');
    posts.value = await res.json();
  }
  catch (error){
    console.log(error);
  }
  finally {
    setTimeout(() => {
      loading.value = false;
    }, 2000)
  }
})

// fetch('https://jsonplaceholder.typicode.com/posts')
//   .then(res => res.json())
//   .then(data => posts.value = data)
//   .catch(e => console.log(e))
//   .finally(() => {
//     setTimeout(() => {
//       loading.value = false;
//     }, 2000)
    
//   })

</script>

<template>
  <LoadingSpinner v-if="loading"/>
  <div class="container" v-else>
    <h1>App</h1>
    <h2>My Favorite Posts {{ favorito }}</h2>

    <PaginatePost 
      class="mb-2" 
      @next="next"
      @prev="prev"
      :inicio="inicio"
      :fin="fin"
      :maxLength="posts.length"
      />

    <BlogPost 
      class="mb-2" 
      v-for="post in posts.slice(inicio, fin)" 
      :key="post.id" 
      :title="post.title" 
      :id="post.id"
      :body="post.body" 
      @cambiarFavoritoNombre="cambiarFavorito" />
  </div>
</template>