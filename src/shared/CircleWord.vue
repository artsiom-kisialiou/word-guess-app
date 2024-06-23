<template>
  <div style="display: flex; align-items: center">
    <div
      class="circle"
      :style="`--total: ${props.characters.length}`"
    >
      <button
        v-for="(char, idx) in chars"
        class="char-button"
        :class="char.hover"
        :style="`--i: ${idx}`"
        @mousedown.left="handleClick(char)"
        @mouseenter.left="hover ? handleMove(char) : ''"
        @mouseup.left="mouseOver"
        @touchstart="handleClick(char)"
        @touchenter="hover ? handleMove(char) : ''"
        @touchcancel="mouseOver"
      >
        <span>
          {{ char.value }}
        </span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";

const chars = ref([]);
const hover = ref(false);
const emit = defineEmits(["update:modelValue", "typeOver"]);
const props = defineProps({
  characters: {
    type: Array,
    default: [],
    required: true,
  },
});

onMounted(() => {
  fillChars();
});

watch(props, () => {
  fillChars();
});

const fillChars = () => {
  chars.value = props.characters.map((char) => {
    return {
      value: char,
      hover: "",
    };
  });
};

const mouseOver = () => {
  hover.value = false;
  chars.value.forEach((char) => {
    char.hover = "";
  });
  setTimeout(() => {
    emit("typeOver");
  }, 500);
};

const handleMove = (char) => {
  char.hover = "char-pressed";
  emit("update:modelValue", char.value);
};

const handleClick = (char) => {
  hover.value = true;
  char.hover = "char-pressed";
  emit("update:modelValue", char.value);
};
</script>

<style scoped>
.circle {
  display: grid;
  grid-template-areas: "layer";
  place-items: center;
  background-color: transparent;
  border: 10px solid #3e4a68;
  height: 294px;
  border-radius: 50%;
  -moz-border-radius: 50%;
  -webkit-border-radius: 50%;
  width: 294px;

  --radius: 20vmin;
  width: calc(2 * var(--radius));
  height: calc(2 * var(--radius));
}

.char-button {
  cursor: pointer;
  grid-area: layer;
  width: 10vmin;
  height: 10vmin;
  border-radius: 50%;
  border: 0;
  box-shadow: 0 2px #b0b0b0;
  touch-action: manipulation;

  display: grid;
  place-items: center;

  background: #ffffff;
  color: #000000;

  --d: calc(var(--i) / var(--total));

  --r-offset: -0.25turn;

  --r-amount: 1turn;

  --r: calc((var(--r-amount) * var(--d)) + var(--r-offset));

  --transform: rotate(var(--r)) translate(var(--radius))
    rotate(calc(-1 * var(--r)));

  transform: var(--transform);
}

.char-button span {
  font-weight: 700;
  font-size: 5vmin;
  text-align: center;
}

.char-pressed {
  background: #e96fa4;
  color: #ffffff;
  box-shadow: 0 2px #af638c;
}
</style>
