<template>
  <div id="app" >
    <MainPage @clicked="hide" />
    <CharacterCreator @back="back" class="hide" />
  </div>
</template>

<script>
import MainPage from './components/MainPage.vue'
import CharacterCreator from './components/CharacterCreator'

export default {
  name: 'App',
  components: {
    MainPage,
    CharacterCreator
  },
  methods: {
    hide() {
      var charEl = document.getElementsByClassName("characterGenerator");
      charEl[0].classList.toggle("hide");
    },
    back() {
      this.hide();
      var homeEl = document.getElementsByClassName("home");
      homeEl[0].classList.toggle("hide");
    }
  },
}
</script>

<style>

  /*Colors are from the nord-gnome-terminal color palette*/
  /*
    black: #2E3440
    grey: #3B4252
    red: #BF616A
    green: #A3BE8C
    yellow: #EBCB8B
    blue: #81A1C1
    purple: #B48EAD
    cyan: #88C0D0
    white:#E5E9F0
  */

  h1 {
    text-align: center;
    color: #A3BE8C;
  }

  .hide {
    display: none;
  }
</style>
