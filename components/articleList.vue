<template>
  <section class="section has-background-light">
    <div class="container">

      <h1 class="title">{{ lastArticlesTitle }}</h1>

      <div class="columns is-centered is-multiline">
        <div class="column is-half" v-for="(article, i) in sortedArticles" :key="i">
          <Article :article="article" />
        </div>
      </div>
    </div>
  </section>
</template>

<script>
  import Article from '~/components/articleCard.vue'

  export default {
    props: ['articles'],
    components: {
      Article
    },
    data () {
      return {
        lastArticlesTitle: 'Derniers articles'
      }
    },
    computed: {
      sortedArticles: function () {
        let articles = this.articles.slice(0);

        articles.sort(function (a1, a2) {
          return a2.published_date - a1.published_date
        });

        return articles;
      }
    }
  }
</script>
<style scoped>
section{
  font-family: FuturaMedium,serif;
}
section h1{
  font-family: FuturaMedium,serif;
  color: rgb(63, 216, 203);
}

@font-face {
  font-family: FuturaMedium;
  src: url("../static/Fonts/FuturaMedium.otf"); 
}
</style>

