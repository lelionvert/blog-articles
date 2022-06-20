<template>
  <nav class="navbar is-pink-background-color" role="navigation" aria-label="main navigation">
    <div class="navbar-brand">
        <a class="navbar-item" href="/">
           <img class ="logo-header" src="../static/lcdlv_logo_blanccomplet.png">
        </a>
        <a v-if="isMinWidth()" class="navbar-item" href="/">
          <h1  class="navbar-item is-white-text has-text-weight-bold title">
            A Software Crafter's Journey
          </h1>
        </a>
      <a class="navbar-burger is-green-background-color burger" aria-label="menu" aria-expanded="false" v-on:click="openMenu()" v-bind:class="{ 'is-active': isNavbarMenuOpened }">
        <span aria-hidden="true"></span>
        <span aria-hidden="true"></span>
        <span aria-hidden="true"></span>
      </a>
    </div>

    <div class="navbar-menu" v-bind:class="{ 'is-active': isNavbarMenuOpened }">
      <div class="navbar-start">
        <!-- <div class="navbar-item has-dropdown is-hoverable">
          <a class="navbar-link">
            Tags
          </a>

          <div class="navbar-dropdown">
            <a class="navbar-item">
              Soft skills
            </a>
            <a class="navbar-item">
              Méthodologie
            </a>
            <a class="navbar-item">
              Bonne pratique
            </a>
          </div>
        </div> -->
      </div>

      <div class="navbar-end">
        <div class="navbar-item">
          <div class="buttons">
            <a class="button is-green-background-color is-white-text button-title" :href="aboutUsLink" target="_blank">
              <strong>&Agrave; propos de nous</strong>
            </a>
            <!--<a class="button is-light" :href="contributionLink" target="_blank">
              Contribuer
            </a>-->
          </div>
        </div>
      </div>

    </div>
  </nav>
</template>

<script>
  export default {
    data() {
      return {
        minWidth : 527, 
        windowWidth: 0,
        isNavbarMenuOpened: false,
        aboutUsLink: 'http://la-combe-du-lion-vert.fr/',
        contributionLink: 'https://github.com/lelionvert/blog-articles/tree/master/articles'
      }
    },
 mounted() {
    this.$nextTick(function() {
      window.addEventListener('resize', this.getWindowWidth);
      this.getWindowWidth();
    })},

    methods: {
      openMenu: function () {
          this.isNavbarMenuOpened = !this.isNavbarMenuOpened;
      },
      getWindowWidth(event) {
        this.windowWidth = document.documentElement.clientWidth;
      },

      isMinWidth(){
        return this.windowWidth > this.minWidth
      }     
      },
      beforeDestroy() {
        window.removeEventListener('resize', this.getWindowWidth);
      }
  }
</script>

<style scoped>
  .is-pink-background-color{
    background-color:  rgb(238,44,130);
  }
  .is-white-text{
    color:  white;
  }
  .is-green-background-color{
    background: rgb(63, 216, 203);
  }
  .button{
    border-color: rgb(63, 216, 203) !important;
  }
  .button:hover{
    color:  white;
  }
  .navbar-burger{
    height: unset !important ;
  }
  .logo-header{
    height:100% !important;
  }
  .navbar-item img {
    max-height: 5.75rem !important;
  }
  .title{
    font-family:FuturaMedium,serif;
    font-weight: 50;
  } 
  .button-title{
    font-family:FuturaMedium,serif;
    font-weight: 50;
  } 


  @font-face {
  font-family: FuturaMedium;
  src: url("../static/Fonts/FuturaMedium.otf"); 
}
</style>
