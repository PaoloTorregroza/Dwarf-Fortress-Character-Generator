<template>
  <div class="home" id="home">
    <h1 class="home-tittle">
      Dwarf Fortres Arena<br/>Character Generator
    </h1>
    <h3 class="home-subtitle">
      Create your character and let him enjoy the glory of victory ... 
    </h3>
    <h4>(Or a painfull dead, I don't care)</h4>

    <div class="startButton">
      <button @click="hide">
        LET'S GO!
      </button>
    </div>

    <div class="gallery">
      <h2>
        Arena gallery
      </h2>

      <div class="images">
        <img src="../assets/ArenaN1_Kar-II_vs_Warhammer.png" alt="Final of the last arena">
        <img src="../assets/ArenaN1_Kar-II_vs_Warhammer.png" alt="">
        <img src="../assets/ArenaN1_Kar-II_vs_Warhammer.png" alt="">
        <img src="../assets/ArenaN1_Kar-II_vs_Warhammer.png" alt="">
      </div>
    </div>

    <div class="about">
      <h4>
        Made by Paolo Torregroza (Paolinsky) (@TorregrozaPaolo on twitter). <br/>
        Con amor para la comunidad de Dwarf Fortress en españo creada por Toen
      </h4>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MainPage',
  methods: {
    hide() {
      var element = document.getElementById("home");
      element.classList.toggle("hide");
      this.$emit("clicked")
    }
  },
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  @import "../assets/sass/font.scss";
  @import "../assets/sass/mainPage.scss";
</style>
