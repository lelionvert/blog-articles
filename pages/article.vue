<template>
        <section v-if="article" class="section has-background-light">

            <div class="container is-widescreen">
                <div class="buttons">
                    <a class="button is-green-background-color is-white-text button-title" href="/">
                        <strong>{{goBackLabel}}</strong>
                    </a>
                    <div class="level-right">
                        <Tags :article="article" />
                    </div>
                </div>
                

                <h1 class="title has-text-centered">{{ article.title }}</h1>

                <p class="subtitle is-6 has-text-centered">
                    <small>
                        {{ publishedByLabel }} {{ article.author }}
                        -
                        <time :datetime="article.published_date">{{ formattedDate }}</time>
                    </small>
                </p>
                <div class="box">
                    <article class="media">
                        <div class="media-content">
                            <client-only>
                                <div class="content">
                                    <nav class="level is-mobile">
                                        <div class="level-right">
                                            <TwitterShareLink :article="article" />
                                        </div>
                                    </nav>

                                    <p>
                                        {{ article.description }}
                                    </p>

                                    <hr class="divider" />

                                    <div v-html="article.content" class="has-text-justified"></div>
                                </div>

                               
                            </client-only>
                        </div>
                    </article>
                </div>

            </div>
        </section>

        <section v-else class="hero is-large has-background-light">
            <div class="hero-body">
                <div class="container has-text-centered">
                    <h1 class="title">
                        404
                    </h1>
                    <h2 class="subtitle">
                        Oups, l'article demand&eacute; n'existe pas !
                    </h2>
                    <h3>
                        <nuxt-link to="/">
                            Retour &agrave; l'accueil
                        </nuxt-link>
                    </h3>
                </div>
            </div>
        </section>
</template>

<script>
    import TwitterShareLink from '~/components/twitterShareLink.vue'
    import Tags from '~/components/tags.vue'

    export default {
        components: {
            TwitterShareLink,
            Tags
        },
        async asyncData({ route }) {
            try {
                const fileName = `${ route.path }.md`;
                const fileContent = await import(`~/articles${fileName}`);

                return {
                    article: {
                        title: fileContent.attributes.title,
                        author: fileContent.attributes.author,
                        published_date: new Date(Date.parse(fileContent.attributes.published_date)),
                        description: fileContent.attributes.description,
                        tags: fileContent.attributes.tags ? fileContent.attributes.tags.split(',') :  '',
                        file_name: fileName,
                        content: fileContent.html,
                        route: route.path
                    }
                };
            } catch (err) {
                return {
                    article: null
                };
            }
        },
        data () {
            return {
                goBackLabel: 'Retour aux articles',
                publishedByLabel: 'par '
            }
        },
        computed: {
            formattedDate: function () {
                return this.article.published_date.toLocaleDateString(undefined, {
                    year: 'numeric', month: 'long', day: 'numeric'
                });
            }
        },
        head () {
            return {
                title: `${ this.article ? this.article.title : 'Erreur 404' } - A Software Crafter's Journey`,
            }
        }
    }
</script>

<style scoped>
 .is-white-text{
    color:  white;
  }
  .is-green-background-color{
    background: rgb(63, 216, 203);
  }
  .buttons{
      justify-content: space-between !important;
  }
  .button{
    border-color: rgb(63, 216, 203) !important;
  }
  .button:hover{
    color:  white;
  }
  .button-title{
    font-family:FuturaMedium,serif;
    font-weight: 50;
  } 
  .level.is-mobile {
    justify-content: right ;
    margin: 0% 2% 0% 2%;
}
section{
  font-family: FuturaMedium,serif;
}

@font-face {
  font-family: FuturaMedium;
  src: url("../static/Fonts/FuturaMedium.otf"); 
}
@font-face {
  font-family: FuturaMedium;
  src: url("../static/Fonts/FuturaMedium.otf"); 
}
</style>
