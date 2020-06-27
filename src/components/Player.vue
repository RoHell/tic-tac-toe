<template>
  <div class="player">
    <div class="player-avatar">
      {{ player.avatar }}
    </div>
    <div class="player-score">
      {{ player.score }}
    </div>
  </div>
</template>

<script>
export default {
  props: {
    player: {
      type: Object,
      required: true
    }
  }
};
</script>

<style lang="scss" scoped>
.player {
  font-size: 5rem;
  margin: auto;
  display: flex;
  justify-content: center;
  align-items: center;
}
.player-avatar {
  border-bottom: 3px solid transparent;
}
.player--active {
  .player-avatar {
    border-bottom: 3px solid gray;
  }
}
.player--pickable {
  .player-avatar {
    cursor: pointer;
  }
}
</style>
