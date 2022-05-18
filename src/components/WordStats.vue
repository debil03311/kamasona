<style scoped>
#word-stats {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: var(--stats-background);
  padding: 1em;
  transition: 200ms;
  overflow: auto;

  display: flex;
  flex-flow: column;
  justify-content: space-between;
  gap: 1em;
}

#word-stats.invisible {
  top: -8px;
  opacity: 0;
  pointer-events: none;
}

#btn-hide-stats {
  width: 100%;
  background-color: var(--open-stats-background);
  color: var(--open-stats-color);
  font-size: .8em;
  font-weight: 600;
  text-transform: uppercase;
  border-radius: var(--border-radius);
  padding: 6px;
  cursor: pointer;
}
</style>

<template>
  <div id="word-stats" :class="{ invisible: isInvisible }">
    <div id="btn-hide-stats"
      @click="$emit('setStatsInvisible', true)">
      Close Stats
    </div>

    <StatsRow v-for="(batch, index) of batches"
      :key="batch"
      :row-index="index"
      :state="batches[index].active"
      :batch="batch.items"
      @rowStateChange="propagateRowStateChange" />
  </div>
</template>

<script>
import StatsRow from "./StatsRow.vue";

export default {
  name: "WordStats",
  components: {
    StatsRow,
  },
  props: {
    batches: Array,
    isInvisible: Boolean,
  },
  methods: {
    propagateRowStateChange(rowIndex, newState) {
      this.$emit("rowStateChange", rowIndex, newState);
    }
  }
}
</script>
