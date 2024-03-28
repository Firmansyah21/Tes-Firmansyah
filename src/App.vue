<template>
  <div class="min-h-screen bg-gray-100">
    <header class="bg-gray-800 text-white py-4">
      <div class="container mx-auto flex justify-between items-center px-4">
        <h1 class="text-xl font-bold">Berita</h1>
        <input v-model="searchQuery" @input="searchNews" type="text" class="px-4 py-2 rounded-md focus:outline-none text-black focus:ring focus:border-blue-300" placeholder="Search News">
      </div>
    </header>
    <div class="container mx-auto py-8">
      <div v-if="loading" class="grid grid-cols-1 md:grid-cols-4 gap-8">
        <div v-for="n in 6" :key="n" class="bg-white rounded-md shadow-md p-4 break-inside avoid-column">
          <div class="animate-pulse flex space-x-4">
            <div class="rounded-md bg-gray-300 h-24 w-24"></div>
            <div class="flex-1 space-y-2 py-1">
              <div class="h-4 bg-gray-300 rounded w-4/5"></div>
              <div class="h-3 bg-gray-300 rounded"></div>
              <div class="h-3 bg-gray-300 rounded w-2/3"></div>
            </div>
          </div>
        </div>
      </div>
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-8">
        <div v-for="(article, index) in articles" :key="article.url" class="bg-white rounded-md shadow-md">
          <img :src="article.urlToImage" alt="News Image" class="rounded-t-md w-full h-64 object-cover">
          <div class="p-4">
            <h2 class="text-lg font-bold mb-2">{{ article.title }}</h2>
         <div class="flex justify-between items-center pb-4 pt-2">
          <p class="text-gray-600">{{ article.author }}</p>
            <p class="text-gray-600">{{ formatDate(article.publishedAt) }}</p>
         </div>
            <p class="text-gray-600">{{ article.description }}</p>
            <a :href="article.url" target="_blank" class="inline-block mt-2 px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">Read More</a>
            <button @click="saveReadArticle(article)" class="inline-block mt-2 ml-2 px-4 py-2 bg-green-500 text-white rounded-md hover:bg-green-600">Save</button>
          </div>
        </div>
      </div>
    </div>
    <div class="container mx-auto py-8">
      <h2 class="text-2xl font-bold mb-7 text-center">Berita Yang Telah Dibaca</h2>
      <div v-if="readArticles.length === 0" class="text-gray-600">Tidak Ada Data</div>
      <div v-else class="grid grid-cols-1 md:grid-cols-3 gap-8">
        <div v-for="readArticle in readArticles" :key="readArticle.url" class="bg-white rounded-md shadow-md">
          <img :src="readArticle.urlToImage" alt="News Image" class="rounded-t-md w-full h-64 object-cover">
          <div class="p-4">
            <h3 class="text-lg font-bold mb-2">{{ readArticle.title }}</h3>
           <div class="flex justify-between items-center pb-4 pt-2 ">
            <p class="text-gray-600">{{ readArticle.author }}</p>
            <p class="text-gray-600">{{ formatDate(readArticle.publishedAt) }}</p>
           </div>
            <p class="text-gray-600">{{ readArticle.description }}</p>
            <a :href="readArticle.url" target="_blank" class="inline-block mt-2 px-4 py-2 bg-blue-500 text-white rounded-md hover:bg-blue-600">Read More</a>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>


<script setup>
import { ref } from 'vue';

const articles = ref([]);
const readArticles = ref([]);
const searchQuery = ref('');
const loading = ref(true);

const fetchNews = async () => {
  try {
    loading.value = true;
    const response = await fetch(`https://newsapi.org/v2/top-headlines?sources=techcrunch&apiKey=60988e0b0ccc4b9daeea96cb812d069d`);
    const data = await response.json();
    articles.value = data.articles;
  } catch (error) {
    console.error(error);
  } finally {
    loading.value = false;
  }
};

const saveReadArticle = (article) => {
  readArticles.value.push(article);
  localStorage.setItem('readArticles', JSON.stringify(readArticles.value));
};

const loadReadArticles = () => {
  const storedArticles = localStorage.getItem('readArticles');
  if (storedArticles) {
    readArticles.value = JSON.parse(storedArticles);
  }
};

const searchNews = () => {
  if (!searchQuery.value) {
    fetchNews();
    return;
  }

  const filteredArticles = articles.value.filter(article =>
    article.title.toLowerCase().includes(searchQuery.value.toLowerCase())
  );
  articles.value = filteredArticles;
};

const formatDate = (dateString) => {
  const options = { year: 'numeric', month: 'short', day: 'numeric', hour: 'numeric', minute: 'numeric' };
  return new Date(dateString).toLocaleDateString('en-US', options);
};

fetchNews();
loadReadArticles();
</script>
