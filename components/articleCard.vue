<template>
    <client-only>
        <a v-if="article" class="box">
            <article class="media">
                <nuxt-link class="media-content has-text-dark" :to="article.route">
                    <div class="content">
                        <p class="title is-4">{{ article.title }}</p>

                        <p class="subtitle is-6">
                            <small>
                                {{ publishedByLabel }} {{ article.author }}
                                -
                                <time :datetime="article.published_date">{{ formattedDate }}</time>
                            </small>
                        </p>

                        <p>
                            {{ article.description }}
                        </p>
                    </div>

                    <nav class="level is-mobile">
                        <div class="level-left">
                            <Tags :article="article" />
                        </div>
                    </nav>
                </nuxt-link>

                <div class="media-right">
                    <TwitterShareLink :article="article" :showLabel="false" />
                </div>
            </article>
        </a>
    </client-only>
</template>

<style scoped>
.box
{
    height: 100%;
}
section h1{
  font-family: FuturaMedium,serif;
  color: rgb(63, 216, 203);
}
 .title{
    font-family: MapleRegular,serif !important;
}
 .subtitle{
  font-family: MapleRegular,serif !important;
}

p{
    font-family: MapleRegular,serif !important;
}

@font-face {
  font-family: MapleRegular;
  src: url("../static/Fonts/Maple-Regular.otf"); 
}

@font-face {
  font-family: FuturaMedium;
  src: url("../static/Fonts/FuturaMedium.otf"); 
}
</style>

<script>
    import TwitterShareLink from '~/components/twitterShareLink.vue'
    import Tags from '~/components/tags.vue'

    export default {
        props: ['article'],
        components: {
            TwitterShareLink,
            Tags
        },
        data () {
            return {
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
        mounted: function () {
            if (this.articleData != null)
                this.article = this.articleData;
        }
    }
</script>
