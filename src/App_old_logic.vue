<template>
  <div>
    <h1 class="alert alert-success">Find All Matches! 😉</h1>
    <div class="content">
      <card
          v-for="(card, idx) in cards"
          :key="card.title + idx"
          :card-item="card"
          :current-index="idx"
          @animationHasEnded="animationHasEnded"
          @open-card="openCard"
      >
      </card>
    </div>
    <div class="content" v-if="showCheatCode">
      <div class="card" v-for="card in cards" :key="card.title + Math.random()">
        {{ card.title }}
      </div>
    </div>

      <div class="grats" v-if="isAllCardsMatched">
        <transition
            enter-active-class="animate__animated animate__bounceInUp"
            leave-active-class="animate__animated animate__bounceOutDown"
            appear
        >
        <h2>
          <span>Niiiiiiiiiiiice! You did it right! Congratulations!!!</span>
          <button class="btn btn-dark" @click="resetTheGame">Reset</button>
        </h2>
        </transition>
      </div>

  </div>
</template>

<script>
import Card from "@/components/Card.vue";

export default {
  name: 'App',
  components: {
    Card
  },
  data() {
    return {
      cards: [],
      isAnimationFinished: true,
      showCheatCode: false,
      cheatWord: 'halfa',
      userWord: ''
    };
  },
  computed: {
    isAllCardsMatched() {
      return (
          this.cards.filter((elem) => elem.isMatched).length === this.cards.length
      );
    },
  },
  methods: {
    openCard(idx) {
      if (this.isAnimationFinished) {
        this.isAnimationFinished = false;
        const existedCard = this.cards.find(
            (elem, index) =>
                elem.isOpened &&
                !elem.isMatched &&
                elem.title === this.cards[idx].title &&
                index !== idx
        );
        const openedCard = this.cards.find(
            (elem, i) => elem.isOpened && !elem.isMatched && i !== idx
        );
        this.cards[idx].isOpened = !this.cards[idx].isOpened;
        if (this.cards[idx].isOpened && existedCard) {
          this.cards[idx].isMatched = true;
          existedCard.isMatched = true;
        } else {
          this.cards[idx].isMatched = false;
          if (openedCard) {
            setTimeout(() => {
              this.cards[idx].isOpened = false;
              openedCard.isOpened = false;
              openedCard.isMatched = false;
            }, 1000);
          }
        }
      }
    },
    animationHasEnded(ev) {
      this.isAnimationFinished = true;
    },
    resetTheGame() {
      this.isAnimationFinished = true;
      this.cards = generateRandomCards();
    },
    async activateCheatCode(ev) {
      this.userWord += ev.key;
      let enteredText = this.userWord.slice(-this.cheatWord.length);
      if (enteredText.toLowerCase() === this.cheatWord) {
        this.showCheatCode = !this.showCheatCode;

        if (this.showCheatCode) {
          await this.$nextTick();
          window.scroll(0, window.scrollMaxY);
        }
      }
    }
  },
  created() {
    this.cards = generateRandomCards();
  },
  mounted() {
    document.onkeydown = ev => this.activateCheatCode(ev);
    setTimeout(() => this.cards.forEach(el => el.isMatched = true), 1000)
  },
}


function generateRandomCards() {
  const nominals = [
    "2",
    "3",
    "4",
    "5",
    "6",
    "7",
    "8",
    "9",
    "10",
    "Jack",
    "Queen",
    "King",
    "Ace",
  ];
  const kinds = ["Hearts", "Clubs", "Diamonds", "Spades"];
  const cards = new Array(16);
  const indexes = [];
  for (let i = 0; i < 16; i++) {
    indexes.push(i);
  }
  const freeIndex = () => {
    let rnd = getRandomNumber(0, 16);
    if (indexes[rnd] !== null) {
      return rnd;
    } else {
      return freeIndex();
    }
  };

  const freeCard = () => {
    const rndCard = getRandomNumber(0, nominals.length);
    const rndKind = getRandomNumber(0, kinds.length);
    const title = `${ nominals[rndCard] } of ${ kinds[rndKind] }`;
    const existedCard = cards.find((elem) => elem.title === title);
    if (!existedCard) {
      return {
        rndCard,
        rndKind,
      };
    } else {
      return freeCard();
    }
  };

  for (let i = 0; i < 8; i++) {
    const rndCard = getRandomNumber(0, nominals.length);
    const rndKind = getRandomNumber(0, kinds.length);

    let rndIndex = freeIndex();
    const newCard = new CardFactory(
        `${ nominals[rndCard] } of ${ kinds[rndKind] }`
    );
    cards[rndIndex] = JSON.parse(JSON.stringify(newCard));
    indexes[rndIndex] = null;

    rndIndex = freeIndex();
    cards[rndIndex] = JSON.parse(JSON.stringify(newCard));
    indexes[rndIndex] = null;
  }
  return cards;
}

function CardFactory(title) {
  this.title = title;
  this.isOpened = false;
  this.isMatched = false;
}

function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min + 1)) + min;
}
</script>


<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 20px;
}

h1,
h2 {
  font-weight: normal;
}

ul {
  list-style-type: none;
  padding: 0;
}

li {
  display: inline-block;
  margin: 0 10px;
}

a {
  color: #42b983;
}

.content {
  display: grid;
  grid-template-columns: repeat(4, 1fr);
  grid-template-rows: repeat(4, 1fr);
  grid-column-gap: 10px;
  grid-row-gap: 10px;
  padding: 20px;
}

.grats {
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  flex-flow: column;
  background: rgba(0, 0, 0, 0.5);
}

.grats > h2 {
  display: flex;
  align-items: center;
  flex-flow: column;
  font-size: 70px;
}

.grats span {
  background: #dff0d8;
}

.grats button {
  width: fit-content;
  margin: 15px 0 0 0;
  font-size: 30px;
}

.card {
  height: 200px;
  margin: 0;
  position: relative;
}
</style>

