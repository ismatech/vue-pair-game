<template>
  <div class="card" @click.stop="onClick">
    <transition
      mode="out-in"
      name="flip-horizontally"
      @after-enter="$emit('animationHasEnded')"
    >
      <div
        v-if="!cardItem.isOpened || (cardItem.opened && !cardItem.isMatched)"
        key="backSide"
        class="card-back-side"
      ></div>

      <div v-else key="frontSide" class="card-front-side">
        <div class="card-header text-left">
          <span>{{ cardItem.value }}</span>
          <img :alt="cardItem.title" :src="imageSource" />
        </div>
        <div class="card-content">
          <img :alt="cardItem.title" :src="imageSource" />
        </div>
        <div class="card-footer text-right">
          <span>{{ cardItem.value }}</span>
          <img :alt="cardItem.title" :src="imageSource" />
        </div>
      </div>
    </transition>
  </div>
</template>

<script>
export default {
  name: "Card",
  props: {
    cardItem: Object,
    currentIndex: Number,
  },
  computed: {
    imageSource() {
      let img = this.cardItem.title.split(" ")[2].toLowerCase();
      switch (img) {
        case "hearts":
          return require("@/assets/img/hearts_icon.png");
        case "spades":
          return require("@/assets/img/spades_icon.png");
        case "clubs":
          return require("@/assets/img/clubs_icon.png");
        case "diamonds":
          return require("@/assets/img/diamonds_icon.png");
      }
    },
  },
  methods: {
    onClick() {
      if (!this.cardItem.isMatched) {
        this.$emit("open-card", this.currentIndex);
      }
    },
  },
};
</script>

<style lang="less" scoped>
.card {
  height: 200px;
  margin: 0;
  position: relative;
  perspective: 1000px;
  cursor: pointer;
  border: none;
  background: none;

  & > div {
    border-radius: 15px;
    border: 2px solid #000;
    -webkit-backface-visibility: hidden !important;
    backface-visibility: hidden !important;
  }

  &-header,
  &-footer {
    height: 20%;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;

    span {
      font-size: 30px;
      margin: 0 0 0 15px;
    }

    img {
      height: 70%;
    }
  }

  &-footer {
    justify-content: right;
  }

  &-content {
    height: 60%;
    display: flex;
    flex-flow: row nowrap;
    align-items: center;
    justify-content: center;

    img {
      //width: 50px;
      height: 70%;
    }
  }

  &-front-side {
    background: #ddd;
    height: 100%;
  }

  &-back-side {
    background: #999999;
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
}

.flip-horizontally-enter {
  transform: rotateY(180deg);
}

.flip-horizontally-enter-active {
  transition: transform 0.5s;
}

.flip-horizontally-leave-active {
  transition: transform 0.5s;
}

.flip-horizontally-leave-to {
  transform: rotateY(180deg);
}
</style>
