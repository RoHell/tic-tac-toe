<template>
  <div class="player-picker" @mouseout="$emit('hover', selectedEmoji)">
    <div
      v-for="(emoji, index) in emojis"
      :key="index"
      class="player-picker__icon"
      :class="{
        'player-picker__icon--active': selectedEmoji === emoji
      }"
      @mouseover="$emit('hover', emoji)"
      @click="selectAvatar(emoji)"
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
  },
  methods: {
    selectAvatar(avatar) {
      this.selectedEmoji = avatar;
      this.$emit("picked", this.selectedEmoji);
    }
  }
};
</script>

<style lang="scss" scoped>
.player-picker {
  display: flex;
  flex-wrap: wrap;
  margin: 0 auto;
  max-height: 195px;
  overflow: auto;
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
