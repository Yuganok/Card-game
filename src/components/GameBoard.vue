<template>
  <div class="board-wrapper">
    <div class="board" :style="{ gridTemplateColumns: `repeat(${size}, 70px)`, gridTemplateRows: `repeat(${rows}, 70px)` }">
      <template v-for="card in cards" :key="card.id">
        <Card
          :card="card"
          :flipped="isFlipped(card)"
          :matched="card.matched"
          @click="onCardClick(card)"
          :style="card.matched ? 'visibility: hidden;' : ''"
        />
      </template>
    </div>
  </div>
</template>

<script>
import Card from './Card.vue';

function shuffle(array) {
  return array.sort(() => Math.random() - 0.5);
}

export default {
  components: { Card },
  props: {
    hideMatched: {
      type: Boolean,
      default: true
    },
    resetKey: {
      type: Number,
      default: 0
    },
    size: {
      type: Number,
      default: 4
    },
    rows: {
      type: Number,
      default: 4
    }
  },
  data() {
    return {
      cards: [],
      opened: [],
      moves: 0,
      lock: false,
      errors: 0,
    };
  },
  watch: {
    resetKey() {
      this.initGame();
    },
    size() {
      this.initGame();
    },
    rows() {
      this.initGame();
    }
  },
  created() {
    this.initGame();
  },
  emits: ['game-over', 'error', 'move'],
  methods: {
    initGame() {
      const icons = [
        '🍎','🍌','🍒','🍇','🍉','🍋','🍓','🍑',
        '🥝','🍍','🥥','🥭','🍈','🍏','🍐','🍊',
        '🍔','🍕','🍟','🌭','🍿','🥨','🥐','🥯',
        '🥞','🧇','🍗','🍖','🍤','🍣','🍱','🍛',
        '🍙','🍚','🍘','🍥','🥮','🍢','🍡','🍧',
        '🍨','🍦','🥧','🧁','🍰','🎂','🍮','🍭',
        '🍬','🍫','🍩','🍪','🥠','🥟','🍯','🥜',
        '🍞','🥖','🥓','🥩','🥚','🧀','🍠','🥔'
      ];
      let cards = [];
      for (let i = 0; i < this.size * this.rows / 2; i++) {
        cards.push({ id: i*2, icon: icons[i], matched: false });
        cards.push({ id: i*2+1, icon: icons[i], matched: false });
      }
      this.cards = shuffle(cards);
      this.opened = [];
      this.moves = 0;
      this.lock = false;
      this.errors = 0;
    },
    isFlipped(card) {
      return this.opened.includes(card) || card.matched;
    },
    onCardClick(card) {
      if (this.lock || this.opened.includes(card) || card.matched) return;
      this.opened.push(card);
      if (this.opened.length === 2) {
        this.lock = true;
        setTimeout(() => {
          this.moves++;
          this.$emit('move', this.moves);
          if (this.opened[0].icon === this.opened[1].icon) {
            this.opened[0].matched = true;
            this.opened[1].matched = true;
            if (this.cards.every(c => c.matched)) {
              this.$emit('game-over', this.moves, this.errors);
            }
          } else {
            this.errors++;
            this.$emit('error', this.errors);
          }
          this.opened = [];
          this.lock = false;
        }, 500);
      }
    },
  },
};
</script>

<style scoped>
.board-wrapper {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100%;
  box-sizing: border-box;
}
.board {
  display: grid;
  grid-gap: 10px;
  justify-content: center;
  align-items: center;
}
</style> 