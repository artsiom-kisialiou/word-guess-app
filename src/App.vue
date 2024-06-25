<template>
  <section>
    <ModalComponent
      :show="modalComponent"
      @refresh="refreshPage"
    />
    <VictoryScreen
      v-if="victory"
      :level="levelNum"
      @next-level="loadLevelByName(1)"
    />
    <MainScreen
      v-if="!victory"
      :levelNum="levelNum"
      :currentLevel="currentLevel"
      :currentLevelData="currentLevelData"
      @on-victory="showVictoryScreen"
    />
  </section>
</template>

<script setup>
import MainScreen from "./components/MainScreen.vue";
import { ref, onBeforeMount } from "vue";
import level1 from "./levels/1.json";
import level2 from "./levels/2.json";
import level3 from "./levels/3.json";
import VictoryScreen from "./components/VictoryScreen.vue";
import ModalComponent from "./components/ModalComponent.vue";

const channel = new BroadcastChannel("tab-activity");
const modalComponent = ref(false);
const currentLevel = ref(0);
const currentLevelData = ref({});
const levelNum = ref(1);
const victory = ref(false);
const levels = ref([level1, level2, level3]);

onBeforeMount(() => {
  if (localStorage.gameData) {
    const gameData = JSON.parse(localStorage.gameData);
    victory.value = gameData.victory;
    levelNum.value = gameData.levelNum;
    currentLevel.value = gameData.currentLevel;
    currentLevelData.value = gameData.currentLevelData;
  } else {
    loadLevelByName(currentLevel.value);
  }
});

channel.addEventListener("message", (event) => {
  if (event.data === "open-new-tab") {
    modalComponent.value = true;
  }
});

document.addEventListener("visibilitychange", function () {
  if (document.hidden) {
    channel.postMessage("open-new-tab");
  }
});

const refreshPage = () => {
  modalComponent.value = false;
  window.location.reload();
};

const setProgressInStorage = () => {
  return localStorage.setItem(
    "gameData",
    JSON.stringify({
      victory: victory.value,
      levelNum: levelNum.value,
      currentLevel: currentLevel.value,
      currentLevelData: currentLevelData.value,
    })
  );
};

const showVictoryScreen = () => {
  victory.value = true;
  setProgressInStorage();
};

const splitWords = (data) => {
  data = data.words.sort((a, b) => a.length - b.length);
  const words = data.map((word) => {
    return {
      chars: word.split(""),
      guessed: false,
    };
  });
  return words;
};

const loadLevelByName = (increment) => {
  victory.value = false;
  currentLevel.value += increment;
  levelNum.value += increment;

  if (currentLevel.value == levels.value.length) {
    currentLevel.value = 0;
  }

  const level = levels.value[currentLevel.value];
  currentLevelData.value = splitWords(level);
};
</script>

<style scoped></style>
