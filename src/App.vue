<template>
  <div id="app">
    <header>
      <h2 class="header-2">Connect4</h2>
      <div class="division-line"></div>
    </header>

    <section>
      <div class="player-turns">
        <h3>Turn</h3>
        <br />
        <div>
          <yellow-circle v-if="turn === 'yellow'" />
          <red-circle v-if="turn === 'red'" />
        </div>
      </div>

      <div class="board">
        <div
          class="column"
          v-for="(columnArr, index) of c4.board"
          v-bind:key="index"
          v-bind:data-column="index"
          v-on:click="moves($event)"
        >
          <div
            class="row"
            v-for="(row, i) of columnArr"
            v-bind:key="i"
            :data-row="i"
          >
            <yellow-circle v-if="row === 'yellow'" />
            <red-circle v-if="row === 'red'" />
          </div>
        </div>
      </div>

      <div class="scores">
        <h3>Score Table</h3>
        <br />
        <div class="score-table">
          <div class="score">
            <red-circle />
            <div class="score-number">{{ redScore }}</div>
          </div>

          <div class="score">
            <yellow-circle />
            <div class="score-number">{{ yellowScore }}</div>
          </div>
        </div>

        <div class="btns">
          <button v-on:click="newGame" id="newGameBtn" type="button">
            New game
          </button>
          <button v-if="isNext" v-on:click="next" id="nextBtn" type="button">
            Next
          </button>
        </div>
      </div>
    </section>
  </div>
</template>

<script>
import YellowCircle from './components/YellowCircle.vue';
import RedCircle from './components/RedCircle.vue';
import Connect4 from './util/Connect4';

export default {
  name: 'App',
  components: {
    YellowCircle,
    RedCircle,
  },
  data() {
    return {
      turn: 'red',
      redScore: 0,
      yellowScore: 0,
      isLock: false,
      isNext: false,
    };
  },
  created: function () {
    this.c4 = new Connect4(6, 7);
    this.players = ['red', 'yellow'];
  },
  methods: {
    moves: function (e) {
      if (this.isLock) return;

      const column = parseInt(e.currentTarget.dataset.column);

      if (this.turn === 'red') {
        console.log(this.c4);

        this.c4.putRed(column);

        if (this.c4.isMoved) {
          this.turn = 'yellow';

          if (this.c4.boardCapacity === 0) {
            this.isLock = true;
            this.isNext = true;
          }
          return;
        }
      }

      if (this.turn === 'yellow') {
        console.log(this.c4);

        this.c4.putYellow(column);

        if (this.c4.isMoved) {
          this.turn = 'red';

          if (this.c4.boardCapacity === 0) {
            this.isLock = true;
            this.isNext = true;
          }
          return;
        }
      }
    },
    newGame: function () {
      this.c4.reset();
      this.redScore = 0;
      this.yellowScore = 0;
      this.isLock = false;
      this.isNext = false;

      const oneNzero = Math.ceil(Math.random() * 2) - 1;
      this.turn = this.players[oneNzero];
    },
    next: function () {
      this.c4.reset();

      const oneNzero = Math.ceil(Math.random() * 2) - 1;

      this.turn = this.players[oneNzero];

      this.isLock = false;
      this.isNext = false;
    },
  },
  watch: {
    turn: function () {
      const checkWin = Connect4.checkWin(this.c4);

      if (checkWin.isWin) {
        if (checkWin.winner === 'red') {
          this.redScore += 1;
          console.log(checkWin);
        }

        if (checkWin.winner === 'yellow') {
          this.yellowScore += 1;
          console.log(checkWin);
        }

        this.isLock = true;
        this.isNext = true;
      }
    },
  },
};
</script>

<style>
@import './style/main.style.css';

.header-2 {
  padding: 0.5em 2em;
}

.division-line {
  border-bottom: 1px solid;
}

section {
  display: flex;
  justify-content: space-evenly;
  margin-top: 1rem;
}

.board {
  display: flex;
  background-color: #2d4bf5;
  flex-direction: column;
  padding: 20px;
  width: 500px;
  box-shadow: -7px 7px 5px 0px rgba(50, 50, 50, 0.75);
  transform: rotate(-90deg);
}

.column {
  display: flex;
  justify-content: space-between;
  padding: 10px;
  flex-direction: row-reverse;
}

.row {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background-color: #1f40f7;
  box-shadow: inset 0px 0px 15px 0px rgba(50, 50, 50, 1);
}

.column:hover {
  background-color: #ffffff3f;
  cursor: pointer;
}

.player-turns {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.scores {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.score-table {
  display: flex;
}

.score {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin: 0.5em;
}

.score-number {
  font-size: 1.5em;
  margin-top: 0.25em;
}

.btns {
  display: flex;
  flex-direction: column;
  margin-top: 1rem;
}

button {
  background-color: #2d4bf5;
  font-family: monospace, Arial, Helvetica, sans-serif;
  color: white;
  padding: 0.5rem 1rem;
  font-size: 1rem;
}

#nextBtn {
  margin-top: 0.5rem;
}
</style>
