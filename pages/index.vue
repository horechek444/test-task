<template>
  <div class="container">
    <h1 class="container__title">Статьи</h1>
    <ul class="container__list">
      <li class="container__item item" v-for="article in articles" :key="article._id">
        <h2 class="item__title">{{ article.title }}</h2>
        <p class="item__text">{{ article.text }}</p>
      </li>
    </ul>
    <form class="form"
          action="#"
          name="edit"
          @submit="handleSubmit"
          noValidate
    >
      <label class="control">
        <input
          class="input input__title"
          type="text" name="title"
          v-model="newTitle"
          @change="handleInputChange" placeholder="Заголовок статьи" minLength="2" maxLength="20"
          required/>
      </label>
      <label class="control">
        <textarea
          class="input input__text" name="text" v-model="newText"
          @change="handleInputChange" placeholder="Текст статьи" minLength="2" maxLength="200" required />
      </label>
      <input
        class="submit"
        type="submit" value="Сохранить" name="submit"
      />
    </form>
  </div>
</template>

<script>
import Api from '@/services/api'

export default {
  data () {
    return {
      articles: null,
      newTitle: "",
      newText: ""
    }
  },
  created () {
    this.getArticles()
  },
  methods: {
    async getArticles () {
      try {
        this.articles = await Api.getArticles()
      } catch (err) {
        console.log(`${err}`)
      }
    },
    handleArticleCreate (inputValue) {
      Api.createArticle(inputValue)
        .then((newArticle) => {
          this.articles = [...this.articles, newArticle]
        })
        .catch((err) => {
          console.log(`${err}`)
        })
    },
    handleSubmit () {
      let title = this.newTitle && this.newTitle.trim();
      let text = this.newText && this.newText.trim();
      if (!title && !text) {
        return;
      }
      let inputValue = {
        title: title,
        text: text
      };
      this.handleArticleCreate(inputValue);
      this.newTitle = "";
      this.newText = "";
    },
    handleArticleDelete (article) {
      Api.deleteArticle(article._id)
        .then(() => {
          this.articles = this.articles.filter((a) => a._id !== article._id)
        })
        .catch((err) => {
          console.log(`${err}`)
        })
    },
  },
}
</script>

<style>
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  color: #35495e;
}

.container__title {
  margin-bottom: 20px;
}

.container__list {
  list-style: none;
  padding: 0;
}

.container__item {
  min-width: 200px;
  min-height: 70px;
  color: #3b8070;
}

.container__item:not(:last-of-type) {
  margin-bottom: 10px;
}

.form {
  display: flex;
  flex-direction: column;
}

</style>
