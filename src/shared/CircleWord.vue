<template>
  <div class="canvas-container">
    <canvas
      ref="drawContainer"
      width="350"
      height="350"
      @pointerdown.left="startDraw"
      @pointermove="draw"
      style="position: absolute"
    >
    </canvas>
    <div
      class="circle"
      :style="`--total: ${props.characters.length}`"
    >
      <button
        v-for="(char, idx) in chars"
        class="char-button"
        :class="char.hover"
        :style="`--i: ${idx}`"
        @pointerdown="handleClick(char)"
        @pointerenter="hover ? handleMove(char) : ''"
        @pointerup="mouseOver"
        @touchmove="handleTouch"
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
const drawContainer = ref(null);
const canvas = ref(null);
const x = ref(null);
const y = ref(null);
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
  canvas.value = drawContainer.value.getContext("2d");
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

const handleTouch = ($event) => {
  console.log($event);
};

const mouseOver = (e) => {
  // drawLine(x.value, y.value, e.offsetX, e.offsetY);

  x.value = 0;
  y.value = 0;
  hover.value = false;
  chars.value.forEach((char) => {
    char.hover = "";
  });
  setTimeout(() => {
    canvas.value.clearRect(
      0,
      0,
      drawContainer.value.width,
      drawContainer.value.height
    );
    emit("typeOver");
  }, 500);
};

const startDraw = (e) => {
  x.value = e.offsetX;
  y.value = e.offsetY;
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

const drawLine = (x1, y1, x2, y2) => {
  canvas.value.beginPath();
  canvas.value.strokeStyle = "#ffffff";
  canvas.value.lineWidth = 2;
  canvas.value.moveTo(x1, y1);
  canvas.value.lineTo(x2, y2);
  canvas.value.stroke();
  canvas.value.closePath();
};

const draw = (e) => {
  if (hover.value) {
    drawLine(x.value, y.value, e.offsetX, e.offsetY);
    x.value = e.offsetX;
    y.value = e.offsetY;
  }
};
</script>

<style scoped>
.canvas-container {
  position: relative;
  display: flex;
  align-items: center;
  justify-content: center;
}
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
