<template>
    <a class="level-item" aria-label="like" :href="linkedInShareUrl" target="_blank">
        <span class="icon is-small">
            <i class="fab fa-linkedin" aria-hidden="true"></i>
        </span>
    </a>
</template>

<script>
    export default {
        props: {
            article: { type: Object}, 
            showLabel: { type: Boolean, default: true }
        },
        data () {
            return {
                shareLabel: 'Partager'
            }
        },
        computed: {
            articleUrl: function () {
                if (process.browser)
                    return `${ window.location.origin }${ this.article.route }`;

                return this.article.route;
            },
            linkedInShareUrl: function () {
                return 'https://www.linkedin.com/shareArticle?mini=true'
                + '&url=' + this.articleUrl
                + '&title=' + this.article.title
                + '&source=https://blog.lacombedulionvert.vercel.app/';
            }
        }
    }
</script>