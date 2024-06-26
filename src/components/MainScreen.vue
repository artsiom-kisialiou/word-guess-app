<template>
  <div class="level-title">Уровень {{ props.levelNum }}</div>
  <div class="main-container">
    <div class="words">
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
    </div>
    <div class="word-container word-input">
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
  const maxWordLength =
    wordChars.value[wordChars.value.length - 1].chars.length;

  if (inputWord.value[inputWord.value.length - 1] == word) {
    return;
  } else {
    if (inputWord.value.length < maxWordLength) {
      inputWord.value.push(word);
    } else {
      return;
    }
  }
};

const compareWord = () => {
  wordChars.value.find((word) => {
    if (word.chars.join("") === inputWord.value.join("")) {
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
    emit("onVictory");
  }
};
</script>

<style scoped>
.level-title {
  margin: 2vmin 0 4vmin;
  font: "VAG World";
  font-weight: 700;
  font-size: 20px;
  line-height: 30px;
  color: #ffffff;
  text-align: center;
}
.main-container {
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
}

.words {
  height: 50%;
}

.word-input {
  height: 10vmin;
}

.word-container {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
  justify-content: center;
  align-items: center;
}

.char-cell {
  background-color: #ffffff;
  text-align: center;
  border-radius: 8px;
  font: "VAG World";
  font-weight: 700;
  font-size: 3vmin;
  width: 5vmin;
  height: 5vmin;
  margin: 2px;
}

.char-cell-guessed {
  background-color: #65bd65;
  color: #ffffff;
}

.word-block {
  text-align: center;
}
@media only screen and (max-width: 600px) {
  .char-cell {
    font-size: 5vmin;
    width: 8vmin;
    height: 8vmin;
  }

  .word-input {
    height: 20vmin;
  }
}
</style>
