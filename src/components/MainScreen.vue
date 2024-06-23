<template>
  <div style="text-align: center">Level {{ props.levelNum }}</div>
  <div class="main-container">
    <div
      v-for="(word, idx) in wordChars"
      :key="idx"
      class="word-container"
    >
      <div
        v-for="(char, i) in word.chars"
        class="char-cell"
        :class="word.guessed ? 'char-cell-guessed' : ''"
        :key="i"
      >
        {{ word.guessed ? char : "" }}
      </div>
    </div>
    <div
      class="word-container"
      style="height: 100px; padding: 20px"
    >
      <div
        v-for="(cell, idx) in inputWord"
        class="char-cell"
        style="color: #000000"
        :key="idx"
      >
        {{ cell }}
      </div>
    </div>
    <div>
      <CircleWord
        :characters="uniqueChars"
        @update:model-value="showWord"
        @type-over="compareWord"
      />
    </div>
  </div>
</template>

<script setup>
import CircleWord from "../shared/CircleWord.vue";
import { ref, watch, onBeforeMount } from "vue";

const emit = defineEmits(["onVictory"]);
const props = defineProps({
  levelNum: {
    type: Number,
    required: true,
  },
  currentLevel: {
    type: Number,
    required: true,
  },
  currentLevelData: {
    type: Array,
    required: true,
  },
});

const uniqueChars = ref([]);
const wordChars = ref([]);
const inputWord = ref([]);

onBeforeMount(() => {
  wordChars.value = props.currentLevelData;
  findUniqueChars();
});

watch(props, () => {
  wordChars.value = props.currentLevelData;
  findUniqueChars();
});

const findUniqueChars = () => {
  const chars = [];
  wordChars.value.forEach((word) => {
    word.chars.forEach((char) => {
      if (!chars.includes(char)) {
        chars.push(char);
      }
    });
  });
  uniqueChars.value = chars;
};

const showWord = (word) => {
  inputWord.value.push(word);
};

const compareWord = () => {
  wordChars.value.find((word) => {
    if (word.chars.join("") === inputWord.value.join("")) {
      console.log("find");
      word.guessed = true;
      return true;
    }
  });
  inputWord.value = [];
  checkProgress();
};

const checkProgress = () => {
  let count = 0;
  wordChars.value.forEach((word) => {
    if (word.guessed) {
      count++;
      localStorage.setItem(
        "gameData",
        JSON.stringify({
          victory: false,
          levelNum: props.levelNum,
          currentLevel: props.currentLevel,
          currentLevelData: wordChars.value,
        })
      );
    }
  });
  if (count === wordChars.value.length) {
    console.log("win");
    emit("onVictory");
  }
};
</script>

<style scoped>
.main-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

.word-container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: space-around;
  align-items: center;
}

.char-cell {
  background-color: #ffffff;
  text-align: center;
  border-radius: 10px;
  font: "VAG World";
  font-weight: 700;
  font-size: 5vmin;
  width: 8vmin;
  height: 8vmin;
  margin: 6px;
}

.char-cell-guessed {
  background-color: #65bd65;
  color: #ffffff;
}

.word-block {
  text-align: center;
}
</style>
