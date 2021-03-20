<template>
  <div class="container">
    <h1 class="container__title">Статьи</h1>
    <ul class="container__list">
      <li class="container__item item" v-for="article in articles" :key="article._id">
        <button class="container__button" @click="onUpdate(article)">Обновить</button>
        <div class="container__cover">
          <h2 class="item__title">{{ article.title }}</h2>
          <p class="item__text">{{ article.text }}</p>
        </div>
        <button class="container__button" @click="handleArticleDelete(article)">Удалить</button>
      </li>
    </ul>
    <form class="form"
          action="#"
          name="edit"
          @submit="updateSubmit"
          noValidate
    >
      <label class="control">
        <input
          class="input input__title"
          type="text" name="title"
          v-model="newTitle"
          placeholder="Заголовок статьи" minLength="2" maxLength="20"
          required/>
      </label>
      <label class="control">
        <textarea
          class="input input__text" name="text"
          v-model="newText"
          placeholder="Текст статьи" minLength="2" maxLength="200"
          required />
      </label>
      <input
        class="submit"
        type="submit" value="Обновить" name="submit" />
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
      newText: "",
      currentArticle: null,
      isCreate: true,
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
    handleArticleUpdate (articleId, inputValue) {
      Api.updateArticle(articleId, inputValue)
        .then(() => {
          this.articles.forEach((article) => {
            if (article._id === articleId) {
              article = inputValue;
            }
          })
        })
        .catch((err) => {
          console.log(`${err}`)
        })
    },
    onUpdate (article) {
      this.newTitle = article.title;
      this.newText = article.text;
      this.currentArticle = article;
    },
    updateSubmit () {
      let inputValue = {
        title: this.newTitle,
        text: this.newText
      };
      this.handleArticleUpdate(this.currentArticle._id, inputValue);
      this.newTitle = "";
      this.newText = "";
    },
    createSubmit () {
      if (!this.newTitle || !this.newText) {
        return;
      }
      let inputValue = {
        title: this.newTitle,
        text: this.newText
      };
      this.handleArticleCreate(inputValue);
      this.newTitle = "";
      this.newText = "";
    },
    getButtonTitle () {
      if (this.isCreate) {
        return "Создать статью";
      } else {
        return "Обновить статью";
      }
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
  display: flex;
  flex-direction: column;
  align-items: center;
  list-style: none;
  padding: 0;
}

.container__cover {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin: 0 20px 0;
}

.container__item {
  display: flex;
  min-width: 200px;
  min-height: 70px;
  color: #3b8070;
}

.container__item:not(:last-of-type) {
  margin-bottom: 10px;
}

.container__button {
  border-radius: 30%;
  width: 80px;
  height: 80px;
}

.form {
  display: flex;
  flex-direction: column;
}

</style>
