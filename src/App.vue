<template>
  <div>
    <h1 class="alert alert-success">Find All Matches! 😉</h1>
    <div class="content">
      <card
          v-for="(card, idx) in cards"
          :key="card.id + '_' + idx"
          :card-item="card"
          :current-index="idx"
          @animationHasEnded="animationHasEnded"
          @open-card="openCard"
      >
      </card>
    </div>
    <div class="content" v-if="showCheatCode">
      <div class="my-card" v-for="card in cards" :key="card.title + Math.random()">
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


// Метод для сортировки массива объектов в случайном порядке
Array.prototype.shuffle = function () {
  const copy = this.slice();
  for (let i = copy.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [copy[i], copy[j]] = [copy[j], copy[i]];
  }
  return copy;
};

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
      if (this.isAllCardsMatched) {
        this.showCheatCode = false;
      }
    },
    animationHasEnded() {
      this.isAnimationFinished = true;
    },
    resetTheGame() {
      this.animationHasEnded();
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
    // setTimeout(() => this.cards.forEach(el => el.isMatched = true), 1000)
  },
}

function generateCards() {
  const cards = [];
  const suits = ['Hearts', 'Diamonds', 'Clubs', 'Spades'];
  const cardValues = ['2', '3', '4', '5', '6', '7', '8', '9', '10', 'J', 'Q', 'K', 'A'];
  for (let i = 0; i < cardValues.length; i++) {
    for (let j = 0; j < suits.length; j++) {
      cards.push({ id: i, value: cardValues[i], suit: suits[j] });
    }
  }
  return cards;
}

function generateRandomCards() {
  let allCards = generateCards().shuffle().shuffle().slice(0, 8);
  const res = [];
  for (let i = 0; i < allCards.length; i++) {
    res.push(new CardFactory(allCards[i]));
    res.push(new CardFactory(allCards[i]));
  }
  return res.shuffle().shuffle();
}

function CardFactory({ id, value, suit }) {
  this.id = id;
  this.value = value;
  this.suit = suit;
  this.title = `${ value } of ${ suit }`;
  this.isOpened = false;
  this.isMatched = false;
}

function getRandomNumber(min, max) {
  return Math.floor(Math.random() * (max - min)) + min;
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
  z-index: 10;
}

.grats {
  position: absolute;
  z-index: 999;
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

.my-card {
  margin: 0;
  border: 2px solid #000;
  position: relative;
}
</style>

