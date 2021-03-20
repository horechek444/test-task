<template>
  <div class="container">
    <h1 class="title">Статьи</h1>
    <ul class="list">
    </ul>
  </div>
</template>

<script>
import Api from '@/services/api'

export default {
  data() {
    return {
      articles: [],
    }
  },
  created() {
    this.getArticles();
  },
  methods: {
    async getArticles() {
      try {
        this.articles.push(await Api.getArticles());
      } catch (err) {
        console.log(`${err}`);
      }
    },
    handleArticleCreate(inputValue) {
      Api.createArticle(inputValue)
        .then((newArticle) => {
          this.articles.push(newArticle);
        })
        .catch((err) => {
          console.log(`${err}`);
        })
    },
    handleArticleDelete(article) {
      Api.deleteArticle(article._id)
        .then(() => {
          this.articles = this.articles.filter((a) => a._id !== article._id);
        })
        .catch((err) => {
          console.log(`${err}`);
        })
    },
  },
};
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  color: #35495e;
}

.list {
  list-style: none;
}
</style>
