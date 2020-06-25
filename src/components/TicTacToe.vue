<template>
  <div class="tic-tac-toe">
    <button @click="newGame" class="action-button">
      new game
    </button>
    <div class="tic-tac-toe__board">
      <Player :player="players['playerOne']" />
      <div class="tic-tac-toe__board-playground">
        <div
          v-for="(player, idx) in boxes"
          :key="idx"
          class="tic-tac-toe__board-playground-box"
          :class="{
            'tic-tac-toe__board-playground-box--disabled':
              player || isWinningCombination || isFinal
          }"
          @click="pick(idx)"
        >
          <div
            class="tic-tac-toe__board-playground-box__content"
            :class="{
              'tic-tac-toe__board-playground-box__content--winner':
                isWinningCombination && winningCombination.includes(idx)
            }"
          >
            {{ player }}
          </div>
        </div>
      </div>
      <Player :player="players['playerTwo']" />
    </div>
    <div class="tic-tac-toe__result-message">{{ message }}</div>
  </div>
</template>

<script>
import Player from "./Player.vue";
export default {
  components: {
    Player
  },
  data() {
    return {
      // eslint-disable-next-line prettier/prettier
      boxes: [
        "", "", "",
        "", "", "",
        "", "", ""
      ],
      message: "",
      round: 1,
      players: {},
      playerOneAvatar: "",
      playerTwoAvatar: ""
    };
  },
  computed: {
    defaultPlayers() {
      return {
        playerOne: {
          id: "playerOne",
          name: "Player One",
          avatar: this.playerOneAvatar || "👦",
          score: 0
        },
        playerTwo: {
          id: "playerTwo",
          name: "Player Two",
          avatar: this.playerTwoAvatar || "👨‍🦲",
          score: 0
        }
      };
    },
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
      return this.turn && this.players
        ? this.players.playerOne
        : this.players.playerTwo;
    },
    currentPlayerId() {
      return this.currentPlayer && this.currentPlayer.id;
    },
    currentPlayerScore() {
      return this.currentPlayer && this.currentPlayer.score;
    },
    currentPlayerAvatar() {
      return this.currentPlayer && this.currentPlayer.avatar;
    },
    currentPlayerCombinations() {
      return this.boxes.reduce((acc, box, idx) => {
        return box && box === this.currentPlayerAvatar ? [...acc, idx] : acc;
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
    showNext() {
      return (this.isWinningCombination && !this.isFinal) || this.isTie;
    },
    isTie() {
      return this.boxes.every(b => b);
    },
    isFinal() {
      return this.currentPlayerScore === 3;
    }
  },
  created() {
    this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
  },
  methods: {
    pick(idx) {
      this.boxes.splice(idx, 1, this.currentPlayerAvatar);
      this.check();
    },
    check() {
      if (this.isWinningCombination) {
        this.setScore();
      } else if (this.isTie) {
        this.message = "It's a tie";
        setTimeout(() => {
          this.message = "";
        }, 2000);
        this.setScore();
      } else {
        this.round++;
      }
    },
    setScore() {
      let players = { ...this.players };
      setTimeout(() => {
        if (!this.isTie) {
          players[this.currentPlayerId].score += 1;
          this.players[this.currentPlayerId] = this.currentPlayer;
        }
        this.next();
      }, 2000);
    },
    next() {
      this.boxes = ["", "", "", "", "", "", "", "", ""];
      if (this.isFinal) {
        this.message = `${this.currentPlayer.avatar} wins! 🎉`;
        this.round = 1;
      }
    },
    newGame() {
      this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
      this.message = "";
    }
  }
};
</script>

<style lang="scss" scoped>
.tic-tac-toe__board {
  display: flex;
}
.tic-tac-toe__board-playground {
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
.tic-tac-toe__result-message {
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
