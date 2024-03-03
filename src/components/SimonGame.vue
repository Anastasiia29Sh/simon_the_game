<template>
  <div class="game">
    <div class="circle-game">
      <button
        v-for="(color, index) in color_btn"
        :key="index"
        :class="[
          'color-btn',
          color,
          is_click_color_btn[color] ? 'color-btn-click' : '',
        ]"
        @click="onClick(color)"
        :disabled="is_no_human_click"
      />
    </div>

    <div class="interface">
      <p><b>Уровень сложности:</b></p>
      <DifficultyLevels @newTime="(new_time) => (time = new_time)" />

      <button class="play" v-if="!is_play" @click="startGame()">Играть</button>

      <div class="info" v-if="is_play">
        <p>
          <b>Уровень - {{ level }}</b>
        </p>
        <p>{{ action }}</p>
      </div>
    </div>
  </div>
</template>

<script>
import DifficultyLevels from "./DifficultyLevels.vue";

export default {
  name: "SimonGame",
  components: {
    DifficultyLevels,
  },
  data() {
    return {
      sequence: [],
      human_sequence: [],
      count_click: 1,
      level: 1,
      time: "1500",

      color_btn: ["red", "green", "blue", "yellow"],
      sound: {
        red: null,
        green: null,
        blue: null,
        yellow: null,
      },

      is_click_color_btn: {
        red: false,
        green: false,
        blue: false,
        yellow: false,
      },
      is_no_human_click: true,
      is_play: false,
      action: "Запоминайте",
    };
  },

  created() {
    const path_blue = require(`@/assets/sounds/blue.mp3`);
    const path_green = require(`@/assets/sounds/green.mp3`);
    const path_red = require(`@/assets/sounds/red.mp3`);
    const path_yellow = require(`@/assets/sounds/yellow.mp3`);
    this.sound.blue = new Audio(path_blue);
    this.sound.green = new Audio(path_green);
    this.sound.red = new Audio(path_red);
    this.sound.yellow = new Audio(path_yellow);
  },

  methods: {
    async startGame() {
      this.nextStep();
      this.is_no_human_click = false;
      this.is_play = true;
      await this.sleep(300);
      this.action = "Ваш ход";
    },

    clickColorBtn(color) {
      this.sound[color].play();
      this.is_click_color_btn[color] = true;
      setTimeout(
        function () {
          this.is_click_color_btn[color] = false;
        }.bind(this),
        300
      );
      if (this.is_no_human_click) this.sequence.push(color);
      else this.human_sequence.push(color);
    },

    async countClickColorBtn() {
      let count = this.count_click;
      while (count > 0) {
        this.nextStep();
        await this.sleep(this.time);
        count--;
      }
      this.is_no_human_click = false;
      this.action = "Ваш ход";
    },

    async nextStep() {
      let random_color =
        this.color_btn[Math.floor(Math.random() * this.color_btn.length)];
      this.clickColorBtn(random_color);
    },

    async nextLevel() {
      await this.sleep(this.time);
      this.sequence = [];
      this.human_sequence = [];
      this.count_click++;
      this.level++;
      this.action = "Запоминайте";
      this.countClickColorBtn();
    },

    sleep(ms) {
      return new Promise((resolve) => setTimeout(resolve, ms));
    },

    reset() {
      this.sequence = [];
      this.human_sequence = [];
      this.count_click = 1;
      this.level = 1;
      this.is_no_human_click = true;
      this.is_play = false;
      alert("Вы проиграли");
    },

    checkGame() {
      if (this.human_sequence.length < this.sequence.length) {
        let arr = this.sequence.slice(0, this.human_sequence.length);
        if (this.human_sequence.toString() !== arr.toString()) this.reset();
      } else if (this.human_sequence.length === this.sequence.length) {
        if (this.human_sequence.toString() === this.sequence.toString()) {
          this.is_no_human_click = true;
          this.nextLevel();
        } else this.reset();
      } else this.reset();
    },

    onClick(color) {
      this.clickColorBtn(color);
      this.checkGame();
    },
  },
};
</script>

<style scoped lang="sass">
.circle-game
  width: 300px
  height: 300px
  display: grid
  grid-template-columns: repeat(2, 1fr)
  gap: 10px

.color-btn
  border: none
  cursor: pointer

.color-btn-click
  -webkit-box-shadow: 0px 0px 25px 10px rgba(0, 0, 0, 1) inset
  -moz-box-shadow: 0px 0px 25px 10px rgba(0, 0, 0, 1) inset
  box-shadow: 0px 0px 25px 10px rgba(0, 0, 0, 1) inset

.red
  background-color: red
  border-top-left-radius: 100%
.green
  background-color: green
  border-top-right-radius: 100%
.blue
  background-color: blue
  border-bottom-left-radius: 100%
.yellow
  background-color: yellow
  border-bottom-right-radius: 100%

.interface
  margin-top: 40px

.play
  display: block
  margin: 0 auto
  margin-top: 40px
  padding: 7px 30px
  color: #ffffff
  border: 1px solid blue
  border-radius: 7px
  background-color: blue
  cursor: pointer
  &:hover
    color: #000000
    background-color: #ffffff

.info
  margin-top: 40px
</style>
