<template>
  <div class="tic-tac-toe">
    <button @click="newGame" class="action-button">
      new game
    </button>
    <div class="tic-tac-toe__board">
      <div class="tic-tac-toe__board-player">
        <PlayerPicker
          :avatar="players['playerOne'].avatar"
          @pick="players['playerOne'].avatar = $event"
        />
        <Player :player="players['playerOne']" />
      </div>
      <div class="tic-tac-toe__board-playground">
        <div
          v-for="(player, idx) in boxes"
          :key="idx"
          class="tic-tac-toe__board-playground-box"
          :class="{
            'tic-tac-toe__board-playground-box--disabled':
              player || isWinner || isFinal
          }"
          @click="pick(idx)"
        >
          <div
            class="tic-tac-toe__board-playground-box__content"
            :class="{
              'tic-tac-toe__board-playground-box__content--winner':
                isWinner && winningCombination.includes(idx)
            }"
          >
            {{ player }}
          </div>
        </div>
      </div>
      <div class="tic-tac-toe__board-player">
        <PlayerPicker
          :avatar="players['playerTwo'].avatar"
          @pick="players['playerTwo'].avatar = $event"
        />
        <Player :player="players['playerTwo']" />
      </div>
    </div>
    <div class="tic-tac-toe__result-message">{{ message }}</div>
  </div>
</template>

<script>
import Player from "./Player.vue";
import PlayerPicker from "./PlayerPicker.vue";
export default {
  components: {
    Player,
    PlayerPicker
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
      players: {}
    };
  },
  computed: {
    defaultPlayers() {
      return {
        playerOne: {
          id: "playerOne",
          name: "Player One",
          avatar: "👦",
          score: 0
        },
        playerTwo: {
          id: "playerTwo",
          name: "Player Two",
          avatar: "👨‍🦲",
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
      return this.combinations.reduce((acc, combination) => {
        const isWinningCombination = combination.every(idx =>
          this.currentPlayerCombinations.includes(idx)
        );
        return isWinningCombination ? combination : acc;
      }, []);
    },
    isWinner() {
      return this.winningCombination.length > 0;
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
      if (this.isWinner) {
        this.players[this.currentPlayerId].score += 1;
        if (this.isFinal) {
          this.message = `${this.currentPlayer.avatar} wins! 🎉`;
          this.round = 1;
        } else {
          this.setTimeoutMessage(`${this.currentPlayer.avatar} scores!`);
        }
      } else if (this.isTie) {
        this.setTimeoutMessage("It's a tie");
      } else {
        this.round++;
      }
    },
    setTimeoutMessage(message) {
      this.message = message;
      setTimeout(() => {
        this.message = "";
        this.clearBoard();
      }, 2000);
    },
    clearBoard() {
      this.boxes = ["", "", "", "", "", "", "", "", ""];
    },
    newGame() {
      this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
      this.message = "";
      this.round = 1;
      this.clearBoard();
    }
  }
};
</script>

<style lang="scss" scoped>
.tic-tac-toe__board {
  display: flex;
  align-items: center;
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
