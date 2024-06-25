<template>
  <div class="win-container">
    <div class="title">
      <div style="font-size: 30px">Уровень {{ level }} пройден</div>
      <div style="font-size: 40px">Изумительно!</div>
    </div>
    <button
      class="next-level"
      @click="nextLevel"
      @touchend="nextLevel"
    >
      <span> Уровень {{ level + 1 }} </span>
    </button>
  </div>
</template>

<script setup>
const emit = defineEmits(["nextLevel"]);

const props = defineProps({
  level: {
    type: Number,
    required: true,
  },
});

const nextLevel = () => {
  console.log("next");
  emit("nextLevel");
};
</script>

<style scoped>
.win-container {
  height: 100%;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}

.title {
  font: "VAG";
  color: #ffffff;
  font-weight: 700;
  text-align: center;
  height: 50vh;
  margin-top: 20vmin;
}
.next-level {
  width: 330px;
  height: 95px;
  cursor: pointer;
  border: 0;
  border-radius: 50px;
  background-color: #65bd65;
  box-shadow: 0 4px #508853;
  color: #ffffff;
}

.next-level span {
  font-size: 30px;
  font-weight: 700;
  text-align: center;
}

.next-level:active {
  box-shadow: none;
  transform: translateY(4px);
}

@media only screen and (max-width: 600px) {
  .title {
    margin-top: 40vmin;
  }

  .next-level {
    width: 250px;
    height: 70px;
    font-size: 20px;
  }
}
</style>
