<template>
  <div class="container">
    <h1>Management Articles from @nurasyifah_</h1>
    <form @submit.prevent="save" class="article-form">
      <input type="text" v-model="form.title" placeholder="Title" /><br />
      <textarea v-model="form.content" placeholder="Content"></textarea><br />
      <button type="submit" class="btn save">Save</button>
    </form>
    <ul class="article-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <strong>{{ article.title }}</strong><br />
        <p>{{ article.content }}</p>
        <button v-if="article.title && article.content" @click="edit(article)" class="btn edit">Edit</button>
        <button v-if="article.title && article.content" @click="deleteArticle(article.id)" class="btn delete">Delete</button>
      </li>
    </ul>
    <button @click="load" class="btn load">Load</button>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      title: '',
      content: '',
    });

    const articles = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/articles');
        articles.value = response.data;
        console.log('Articles loaded:', response.data);
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/articles/${form.id}`
          : 'http://localhost:3000/articles';
        const method = form.id ? 'put' : 'post';
        console.log(`Saving articles by method: ${method}, url: ${url}, data:`, form);
        const response = await axios({
          method: method,
          url: url,
          data: { title: form.title, content: form.content },
        });
        console.log('Articles saved:', response.data);

        if (form.id) {
          // Update the articles in the articles array with the new data from the server response
          articles.value = articles.value.map((article) =>
            article.id === form.id ? response.data : article
          );
        } else {
          // Add a new article with the ID from the server response
          articles.value.push(response.data);
        }

        // Reset form
        form.id = null;
        form.title = '';
        form.content = '';
      } catch (error) {
        console.error('Error saving article:', error);
      }
    }

    async function deleteArticle(id) {
      try {
        await axios.delete(`http://localhost:3000/articles/${id}`);
        articles.value = articles.value.filter(article => article.id !== id);
        console.log(`Article with id ${id} deleted`);
      } catch (error) {
        console.error('Error deleting article:', error);
      }
    }

    function edit(article) {
      form.id = article.id;
      form.title = article.title;
      form.content = article.content;
    }

    onMounted(load);

    return { form, articles, save, edit, load, deleteArticle };
  },
};
</script>

<style>
body {
  background-color: skyblue;
}

.container {
  font-family: 'Goudy Old Style', sans-serif;
  width: 100%; 
  max-width: 800px; 
  margin: 40px auto; 
  padding: 20px;
  background-color: #87ceeb; 
  border-radius: 8px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
}

h1 {
  font-family: 'Edwardian Script ITC', sans-serif;
  color: #000; 
  text-align: center;
}

.article-form {
  margin-bottom: 20px;
}

.article-form input,
.article-form textarea {
  font-family: 'Goudy Old Style', sans-serif;
  width: 100%;
  padding: 10px;
  margin: 5px 0;
  border: 1px solid #f1c40f; 
  border-radius: 4px;
}

.article-form .btn {
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #f1c40f; 
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-align: center;
}

.article-form .btn:hover {
  background-color: #e1b700; 
}

.article-list {
  list-style: none;
  padding: 0;
}

.article-item {
  background-color: #e6f7ff; 
  border: 1px solid #f1c40f;
  border-radius: 4px;
  margin: 10px 0;
  padding: 10px;
}

.article-item strong {
  color: #f1c40f; 
  font-size: 1.2em;
}

.article-item p {
  color: #333;
}

.article-item .btn {
  margin-top: 10px;
  padding: 5px 10px;
  background-color: #f1c40f;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
}

.article-item .btn:hover {
  background-color: #e1b700; 
}

.btn.load {
  font-family: 'Goudy Old Style', sans-serif;
  display: block;
  width: 100%;
  padding: 10px;
  background-color: #f1c40f; 
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  text-align: center;
  margin-top: 20px;
}

.btn.load:hover {
  background-color: #e1b700; 
}
.btn.save {
  font-family: 'Goudy Old Style', sans-serif;
}
</style>