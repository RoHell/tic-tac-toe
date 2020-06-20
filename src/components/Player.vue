<template>
  <div class="player">
    {{ player.avatar }}
    {{ player.score }}
  </div>
</template>

<script>
export default {
  props: {
    player: {
      type: Object,
      default: () => ({})
    }
  }
};
</script>

<style lang="scss" scoped>
.player {
  font-size: 5rem;
  margin: auto;
}
</style>