<template>
  <div id="app">
    <button @click="newGame" class="action-button">
      new game
    </button>
    <div class="board">
      <Player :player="players['playerOne']" />
      <Playground :players="players" @pick="onPick" @win="onWin" @tie="onTie" />
      <Player :player="players['playerTwo']" />
    </div>
    <div class="message">{{ message }}</div>
  </div>
</template>

<script>
import Player from "./components/Player.vue";
import Playground from "./components/Playground.vue";
export default {
  components: {
    Player,
    Playground
  },
  data() {
    return {
      message: "",
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
    }
  },
  created() {
    this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
  },
  methods: {
    onPick(player) {
      this.players[player.id] = player;
    },
    newGame() {
      this.players = JSON.parse(JSON.stringify(this.defaultPlayers));
      this.message = "";
    },
    onWin(player) {
      this.message = `${player.avatar} wins! 🎉`;
    },
    onTie() {
      this.message = "It's a tie";
      setTimeout(() => {
        this.message = "";
      }, 2000);
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
