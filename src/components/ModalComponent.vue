<template>
  <div
    ref="myModal"
    class="modal"
  >
    <div class="trapezoid"></div>
    <div class="modal-content">
      <div class="header-wrap">
        <div class="model-header">
          <div class="modal-title">Две вкладки с игрой?</div>
        </div>
      </div>
      <div class="modal-text">
        <p>
          Похоже, игра открыта в нескольких вкладках браузера. Чтобы продолжить
          играть в этой вкладке, обновите страницу.
        </p>
      </div>
      <button
        class="refresh-btn"
        @click="refreshPage"
        @touchend="refreshPage"
      >
        <span>Обновить</span>
      </button>
    </div>
  </div>
</template>

<script setup>
import { ref, watch, onMounted } from "vue";
const emit = defineEmits(["refresh"]);
const props = defineProps({
  show: {
    type: Boolean,
    required: true,
  },
});

const myModal = ref(null);

onMounted(() => {
  showModal();
});

watch(props, () => {
  showModal();
});

const showModal = () => {
  if (props.show) {
    myModal.value.style.display = "block";
  } else {
    myModal.value.style.display = "none";
  }
};

const refreshPage = () => {
  emit("refresh");
};
</script>

<style scoped>
.modal {
  display: none;
  position: fixed;
  z-index: 1;
  padding-top: 20vh;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
}

.modal-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  background-color: #fefefe;
  color: #000;
  margin: auto;
  padding: 0px 20px 20px 20px;
  border: 1px solid #888;
  width: 530px;
  border-radius: 30px;
}
.refresh-btn {
  width: 330px;
  height: 60px;
  cursor: pointer;
  border: 0;
  border-radius: 50px;
  background-color: #65bd65;
  box-shadow: 0 4px #508853;
  color: #ffffff;
}

.refresh-btn span {
  font-size: 30px;
  font-weight: 700;
  text-align: center;
}

.refresh-btn:active {
  box-shadow: none;
  transform: translateY(4px);
}

.model-header {
  top: 0;
  width: 430px;
  height: 100px;
  background-color: #ec6b3a;
  margin: 0 23px;
  margin-top: -20px;
  -webkit-clip-path: polygon(0% 0%, 100% 0, 100% 60%, 50% 100%, 0 60%);
  clip-path: polygon(0% 0%, 100% 0, 100% 60%, 50% 100%, 0 60%)
    inset(20 px 20 px 50 px 20 px);
  display: flex;
  text-align: center;
  justify-content: center;
  align-items: center;
}

.header-wrap {
  filter: drop-shadow(0 4px #9a4626);
}

.trapezoid {
  margin: auto;
  border-bottom: 20px solid #9a4626;
  border-left: 25px solid transparent;
  border-right: 25px solid transparent;
  height: 0;
  width: 430px;
}

.modal-title {
  width: 300px;
  text-align: center;
  color: #fff;
  font-size: 40px;
  font-weight: 700;
  line-height: 40px;
  margin-top: -15px;
}

.modal-text {
  line-height: 38px;
  font-weight: 700;
  font-size: 30px;
  color: #4d4d4d;
  text-align: center;
}

@media only screen and (max-width: 600px) {
  .modal-content {
    width: 70vw;
  }

  .refresh-btn {
    width: 100%;
    height: 50px;
  }

  .model-header {
    width: 50vw;
  }
  .modal-title {
    font-size: 6vmin;
  }

  .modal-text {
    line-height: 25px;
    font-size: 5vmin;
  }

  .trapezoid {
    width: 50vw;
  }
}
</style>
