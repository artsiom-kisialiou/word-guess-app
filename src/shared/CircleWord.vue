<template>
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
      @mouseup.left="actionOver"
      @touchstart="handleClick(char)"
      @touchmove="handleTouch"
      @touchend="actionOver"
    >
      <span>
        {{ char.value }}
      </span>
    </button>
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

const handleTouch = (e) => {
  const xPos = e.changedTouches[0].pageX;
  const yPos = e.changedTouches[0].pageY;
  const element = document.elementFromPoint(xPos, yPos);

  if (element.classList.contains("char-button")) {
    const idx = element.style.getPropertyValue("--i");
    chars.value[idx].hover = "char-pressed";
    emit("update:modelValue", chars.value[idx].value);
  }
};

const actionOver = () => {
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
  border-radius: 50%;
  -moz-border-radius: 50%;
  -webkit-border-radius: 50%;

  --radius: 15vmin;
  width: calc(2 * var(--radius));
  height: calc(2 * var(--radius));
}
.char-button {
  cursor: pointer;
  grid-area: layer;
  width: 6vmin;
  height: 6vmin;
  border-radius: 50%;
  border: 0;
  box-shadow: 0 2px #b0b0b0;
  touch-action: manipulation;

  display: grid;
  place-items: center;

  background: #ffffff;
  color: #4d4d4d;

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
  font-size: 3vmin;
  text-align: center;
}

.char-pressed {
  background: #e96fa4;
  color: #ffffff;
  box-shadow: 0 2px #af638c;
}

@media only screen and (max-width: 600px) {
  .circle {
    --radius: 20vmin;
    width: calc(2 * var(--radius));
    height: calc(2 * var(--radius));
  }

  .char-button {
    width: 10vmin;
    height: 10vmin;
  }

  .char-button span {
    font-size: 5vmin;
  }
}
</style>
