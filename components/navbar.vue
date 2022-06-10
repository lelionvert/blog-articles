<template>
  <nav class="navbar is-pink-background-color" role="navigation" aria-label="main navigation">
    <div class="navbar-brand">
        <a class="navbar-item" href="/">
          <img class ="logo-header" src="https://la-combe-du-lion-vert.fr/wp-content/themes/la-combe-du-lion-vert/images/logo.svg">
          <h1 class="navbar-item is-white-text has-text-weight-bold is-family-monospace">
            A Software Crafter's Journey
          </h1>
        </a>
        

      <a class="navbar-burger burger" aria-label="menu" aria-expanded="false" v-on:click="openMenu()" v-bind:class="{ 'is-active': isNavbarMenuOpened }">
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
            <a class="button is-green-background-color is-white-text" :href="aboutUsLink" target="_blank">
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
        isNavbarMenuOpened: false,
        aboutUsLink: 'http://la-combe-du-lion-vert.fr/',
        contributionLink: 'https://github.com/lelionvert/a-software-crafter-s-journey/tree/master/articles'
      }
    },
    methods: {
      openMenu: function () {
          this.isNavbarMenuOpened = !this.isNavbarMenuOpened;
      }
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
  .logo-header{
    width:100px; 
    height:100px;
  }
</style>