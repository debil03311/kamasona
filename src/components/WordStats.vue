<style scoped>
#word-stats {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: var(--stats-background);
  overflow: auto;
  padding: 1em;

  display: grid;
  grid-template-columns: repeat(auto-fill, 128px);
  justify-content: center;
  gap: 1em;
  transition: 200ms;
}

#word-stats.invisible {
  top: -8px;
  opacity: 0;
  pointer-events: none;
}
</style>

<template>
  <div id="word-stats" :class="{ invisible: isInvisible }">
    <StatsItem v-for="word of stats"
      :key="word"
      :item="word" />
  </div>
</template>

<script>
import StatsItem from "./StatsItem.vue";

export default {
  name: "WordStats",
  components: {
    StatsItem,
  },
  props: {
    stats: Array,
    isInvisible: Boolean,
  }
}
</script>
