<template>
  <div class="main-container">
    <div class="word-block">{{ inputWord }}</div>
    <CircleWord
      :characters="chars"
      @update:model-value="showWord"
      @type-over="compareWord"
    />
  </div>
</template>

<script setup>
import CircleWord from "../shared/CircleWord.vue";
import { ref } from "vue";

const chars = ref([]);
const inputWord = ref("");
chars.value = ["m", "a", "r", "s", "e", "f"];

const showWord = (word) => {
  inputWord.value += word;
};

const compareWord = () => {
  console.log(inputWord.value);
};
</script>

<style scoped>
.main-container {
  display: flex;
  flex-direction: column;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
  height: 100vh;
}

.word-block {
  text-align: center;
}
</style>
