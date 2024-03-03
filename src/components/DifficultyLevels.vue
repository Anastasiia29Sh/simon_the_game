<template>
  <div class="difficulty-levels">
    <div v-for="(level, index) in difficulty_levels" :key="index">
      <input
        type="radio"
        :id="level.name"
        :name="level.name"
        :value="level.value"
        v-model.number="time"
      />
      <label :for="level.name">{{ level.title }}</label>
    </div>
  </div>
</template>

<script>
export default {
  name: "DifficultyLevels",
  data() {
    return {
      time: 1500,
      difficulty_levels: [
        { name: "easy", title: "Легкий", value: 1500 },
        { name: "average", title: "Средний", value: 1000 },
        { name: "difficult", title: "Сложный", value: 400 },
      ],
    };
  },
  watch: {
    time: function () {
      this.$emit("newTime", this.time);
    },
  },
};
</script>

<style scoped lang="sass">
.difficulty-levels
  display: flex
  flex-direction: row
  justify-content: space-between
</style>
