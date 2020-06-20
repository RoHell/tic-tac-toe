<template>
  <div id="app">
    <button @click="newGame" class="action-button">
      new game
    </button>
    <div class="board">
      <div class="player">
        {{ playerOne }}
        {{ playerOneScore }}
      </div>
      <div class="playground">
        <div
          v-for="(player, idx) in boxes"
          :key="idx"
          class="playground-box"
          :class="{
            'playground-box--disabled': player || isWinningCombination
          }"
          @click="pick(idx)"
        >
          <div
            class="playground-box__content"
            :class="{
              'playground-box__content--winner':
                isWinningCombination && winningCombination.includes(idx)
            }"
          >
            {{ player }}
          </div>
        </div>
      </div>
      <div class="player">
        {{ playerTwo }}
        {{ playerTwoScore }}
      </div>
    </div>
    <button v-if="showNext" @click="next" class="action-button">
      next
    </button>
    <div class="message" v-if="isFinal">{{ `${currentPlayer} wins! 🎉` }}</div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      // eslint-disable-next-line prettier/prettier
      boxes: [
        "", "", "",
        "", "", "",
        "", "", ""
      ],
      round: 1,
      message: "",
      playerOne: "👦",
      playerTwo: "👨‍🦲",
      playerOneScore: 0,
      playerTwoScore: 0
    };
  },
  computed: {
    combinations() {
      return [
        [0, 1, 2],
        [3, 4, 5],
        [6, 7, 8],
        [0, 3, 6],
        [1, 4, 7],
        [2, 5, 8],
        [0, 4, 8],
        [2, 4, 6]
      ];
    },
    turn() {
      return this.round % 2 === 0;
    },
    currentPlayer() {
      return this.turn ? this.playerOne : this.playerTwo;
    },
    currentPlayerCombinations() {
      return this.boxes.reduce((acc, box, idx) => {
        return box && box === this.currentPlayer ? [...acc, idx] : acc;
      }, []);
    },
    winningCombination() {
      const combination = this.combinations.filter(combination => {
        return combination.every(idx =>
          this.currentPlayerCombinations.includes(idx)
        );
      });
      return combination[0] || [];
    },
    isWinningCombination() {
      return this.winningCombination.length > 0;
    },
    isFinal() {
      return (this.playerOneScore || this.playerTwoScore) === 4;
    },
    showNext() {
      return (this.isWinningCombination && !this.isFinal) || this.isTie;
    },
    isTie() {
      return this.boxes.every(b => b);
    }
  },
  methods: {
    pick(idx) {
      this.boxes.splice(idx, 1, this.currentPlayer);
      this.check();
    },
    check() {
      if (this.isWinningCombination) {
        this.setScore();
      } else {
        this.round++;
      }
    },
    setScore() {
      if (this.currentPlayer === this.playerOne) {
        this.playerOneScore += 1;
      } else {
        this.playerTwoScore += 1;
      }
    },
    next() {
      this.boxes = ["", "", "", "", "", "", "", "", ""];
    },
    newGame() {
      this.playerOneScore = 0;
      this.playerTwoScore = 0;
      this.round = 1;
      this.message = "";
      this.next();
    }
  }
};
</script>

<style lang="scss">
#app {
  font-family: monospace;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
}
.board {
  display: flex;
}
.player {
  font-size: 5rem;
  margin: auto;
}
.playground {
  width: 306px;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 100px);
  grid-gap: 3px;
  display: grid;
  background-color: #ccc;
  margin: 60px auto;

  &-box {
    background: #fff;
    display: flex;
    justify-content: center;
    align-items: center;
    width: 100px;
    cursor: pointer;
  }

  &-box:hover {
    background: #fafafa;
  }

  &-box--disabled {
    pointer-events: none;
    cursor: default;
  }

  &-box__content {
    font-size: 4rem;
    color: #444;
  }

  &-box__content--winner {
    color: #000;
    animation: happy 1s linear infinite;
  }
}
@keyframes happy {
  25% {
    transform: rotate(-20deg);
  }
  75% {
    transform: rotate(20deg);
  }
}
.message {
  font-size: 6rem;
  margin: 40px auto;
}
.action-button {
  cursor: pointer;
  margin: 10px;
  padding: 5px 10px;
  font-size: 1rem;
}
</style>
