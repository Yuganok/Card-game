<script>
import GameBoard from './components/GameBoard.vue';

export default {
  components: { GameBoard },
  data() {
    return {
      gameOver: false,
      moves: 0,
      errors: 0,
      round: 1,
      resetKey: 0,
      size: 4, // columns
      rows: 4, // rows
    };
  },
  methods: {
    onGameOver(moves, errors) {
      this.gameOver = true;
      this.moves = moves;
      this.errors = errors;
    },
    onError(errors) {
      this.errors = errors;
    },
    onMove(moves) {
      this.moves = moves;
    },
    restart() {
      this.gameOver = false;
      this.moves = 0;
      this.errors = 0;
      this.round++;
      this.resetKey++;
    },
    selectDifficulty(level) {
      let newCols = 4;
      let newRows = 4;
      if (level === 'easy') { newCols = 4; newRows = 4; }
      if (level === 'medium') { newCols = 6; newRows = 6; }
      if (level === 'hard') { newCols = 7; newRows = 6; }
      if (this.size !== newCols || this.rows !== newRows) {
        this.size = newCols;
        this.rows = newRows;
        this.resetKey++;
        this.gameOver = false;
        this.moves = 0;
        this.errors = 0;
        this.round++;
      }
    },
  },
};
</script>

<template>
  <div>
    <div class="main-content">
      <GameBoard
        v-if="!gameOver"
        :hideMatched="true"
        :resetKey="resetKey"
        :size="size"
        :rows="rows"
        @game-over="onGameOver"
        @error="onError"
        @move="onMove"
      />
      <div v-else class="stats">
        <h2>Game Over!</h2>
        <p>Moves: {{ moves }}</p>
        <p>Errors: {{ errors }}</p>
        <button @click="restart">Play Again</button>
      </div>
    </div>
    <div class="status-bar">
      <span>Moves: {{ moves }}</span>
      <span>Round: {{ round }}</span>
      <span>Errors: {{ errors }}</span>
      <span class="difficulty-bar">
        <button :class="{active: size === 4}" @click="selectDifficulty('easy')">Easy</button>
        <button :class="{active: size === 6}" @click="selectDifficulty('medium')">Medium</button>
        <button :class="{active: size === 7 && rows === 6}" @click="selectDifficulty('hard')">Hard</button>
      </span>
    </div>
  </div>
</template>

<style>
html, body, #app {
  height: 100%;
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  overflow-x: hidden;
}
#app {
  width: 100vw;
  min-width: 100vw;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
}
.main-content {
  flex: 1 1 auto;
  display: flex;
  justify-content: center;
  align-items: center;
  height: calc(100vh - 48px); /* 48px — высота статус-бара */
  width: 100vw;
  min-width: 100vw;
}
body {
  font-family: Arial, sans-serif;
}
.stats {
  text-align: center;
  margin-top: 50px;
}
button {
  padding: 10px 20px;
  font-size: 1em;
  border-radius: 6px;
  border: none;
  background: #3498db;
  color: #fff;
  cursor: pointer;
  margin-top: 20px;
}
button:hover {
  background: #217dbb;
}
.status-bar {
  position: fixed;
  left: 0;
  right: 0;
  bottom: 0;
  background: #222;
  color: #fff;
  padding: 12px 0;
  display: flex;
  justify-content: center;
  gap: 40px;
  font-size: 1.1em;
  letter-spacing: 1px;
  z-index: 10;
  align-items: center;
}
.difficulty-bar {
  display: flex;
  gap: 8px;
  margin-left: 30px;
}
.difficulty-bar button {
  padding: 4px 14px;
  font-size: 1em;
  border-radius: 6px;
  border: none;
  background: #3498db;
  color: #fff;
  cursor: pointer;
  transition: background 0.2s;
}
.difficulty-bar button.active,
.difficulty-bar button:hover {
  background: #217dbb;
}
</style>
