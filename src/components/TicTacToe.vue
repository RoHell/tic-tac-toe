<template>
  <div class="tic-tac-toe">
    <button @click="newGame" class="action-button">
      new game
    </button>
    <div class="tic-tac-toe__board">
      <div class="tic-tac-toe__board-player">
        <Player
          :player="playerOne"
          :class="{
            'player--active': currentPlayer.id === PLAYER_ONE_ID,
            'player--pickable': isFirstRound
          }"
          @click.native="toggleShowAvatars(PLAYER_ONE_ID)"
        />
        <PlayerPicker
          v-if="playerOneAvatarsShow"
          :avatar="playerOne.avatar"
          @hover="setPlayerAvatar($event, PLAYER_ONE_ID)"
          @picked="onAvatarPicked($event, PLAYER_ONE_ID)"
        />
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
        <Player
          :player="playerTwo"
          :class="{
            'player--active': currentPlayer.id === PLAYER_TWO_ID,
            'player--pickable': isFirstRound
          }"
          @click.native="toggleShowAvatars(PLAYER_TWO_ID)"
        />
        <PlayerPicker
          v-if="playerTwoAvatarsShow"
          :avatar="playerTwo.avatar"
          @hover="setPlayerAvatar($event, PLAYER_TWO_ID)"
          @picked="onAvatarPicked($event, PLAYER_TWO_ID)"
        />
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
      players: null,
      playerOneAvatarsShow: false,
      playerTwoAvatarsShow: false,
      isPlayerOneStarting: true
    };
  },
  computed: {
    PLAYER_ONE_ID: () => "playerOne",
    PLAYER_TWO_ID: () => "playerTwo",
    defaultPlayers() {
      return {
        [this.PLAYER_ONE_ID]: {
          id: this.PLAYER_ONE_ID,
          name: "Player One",
          avatar: "🙆",
          score: 0
        },
        [this.PLAYER_TWO_ID]: {
          id: this.PLAYER_TWO_ID,
          name: "Player Two",
          avatar: "🙅‍♂️",
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
      return this.isPlayerOneStarting
        ? this.round % 2 !== 0
        : this.round % 2 === 0;
    },
    currentPlayer() {
      return this.turn && this.players
        ? this.players[this.PLAYER_ONE_ID]
        : this.players[this.PLAYER_TWO_ID];
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
    playerOne() {
      return this.players && this.players[this.PLAYER_ONE_ID];
    },
    playerTwo() {
      return this.players && this.players[this.PLAYER_TWO_ID];
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
    },
    isFirstRound() {
      return this.round === 1;
    }
  },
  created() {
    this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
  },
  methods: {
    pick(idx) {
      this.playerOneAvatarsShow = false;
      this.playerTwoAvatarsShow = false;
      this.boxes.splice(idx, 1, this.currentPlayerAvatar);
      this.check();
    },
    check() {
      if (this.isWinner) {
        this.players[this.currentPlayerId].score += 1;
        if (this.isFinal) {
          this.message = `${this.currentPlayer.avatar} wins! 🎉`;
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
        this.isPlayerOneStarting = !this.isPlayerOneStarting;
        this.clearBoard();
      }, 2000);
    },
    clearBoard() {
      this.boxes = ["", "", "", "", "", "", "", "", ""];
    },
    newGame() {
      this.players[this.PLAYER_ONE_ID].score = 0;
      this.players[this.PLAYER_TWO_ID].score = 0;
      this.message = "";
      this.round = 1;
      this.clearBoard();
    },
    toggleShowAvatars(playerId) {
      const show = `${playerId}AvatarsShow`;
      if (this.isFirstRound) {
        this[show] = !this[show];
      }
    },
    onAvatarPicked(avatar, playerId) {
      this.setPlayerAvatar(avatar, playerId);
      this.toggleShowAvatars(playerId);
    },
    setPlayerAvatar(avatar, playerId) {
      this.players[playerId].avatar = avatar;
    }
  }
};
</script>

<style lang="scss" scoped>
.tic-tac-toe__board {
  display: flex;
  justify-content: center;
  margin-top: 20px;
}
.tic-tac-toe__board-player {
  margin: 0 auto;
  flex: 1;
}
.tic-tac-toe__board-playground {
  width: 306px;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(3, 100px);
  grid-gap: 3px;
  display: grid;
  background-color: #ccc;
  margin: 0 20px;
  align-self: center;

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
