<template>
  <div class="app" id="app">
    <h1 class="title">Simon The Game</h1>
    <SimonGame />
  </div>
</template>

<script>
import SimonGame from "./components/SimonGame.vue";

export default {
  name: "App",
  components: {
    SimonGame,
  },
};
</script>

<style scoped lang="sass">
.app
  width: 300px
  margin: 40px auto
  color: #ffffff
  text-align: center

.title
  margin-bottom: 40px
</style>
