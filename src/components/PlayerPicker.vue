<template>
  <div class="player-picker" @mouseout="$emit('pick', selectedEmoji)">
    <div
      v-for="(emoji, index) in emojis"
      :key="index"
      class="player-picker__icon"
      :class="{
        'player-picker__icon--active': selectedEmoji === emoji
      }"
      @mouseover="$emit('pick', emoji)"
      @click="selectedEmoji = emoji"
    >
      {{ emoji }}
    </div>
  </div>
</template>

<script>
import EMOJIS from "../assets/emojis.json";

export default {
  props: {
    avatar: {
      type: String,
      required: true
    }
  },
  computed: {
    emojis: () => EMOJIS
  },
  data() {
    return {
      selectedEmoji: ""
    };
  },
  mounted() {
    this.selectedEmoji = this.avatar;
  }
};
</script>

<style lang="scss" scoped>
.player-picker {
  display: flex;
  flex-wrap: wrap;
}
.player-picker__icon {
  font-size: 1.5rem;
  border-bottom: 1px solid transparent;
  cursor: pointer;

  &:hover {
    border-bottom: 1px solid gray;
  }
}
.player-picker__icon--active {
  border-bottom: 1px solid gray;
}
</style>
