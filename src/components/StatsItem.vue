<style scoped>
.stats-item {
  border: solid 1px var(--stats-item-border);
  border-radius: var(--border-radius);
  padding-bottom: var(--padding);

  display: flex;
  flex-flow: column nowrap;
}

.stats-word {
  background-color: var(--stats-item-border);
  font-weight: 600;
  border-radius: var(--border-radius) var(--border-radius) 0 0;
  padding: 4px;
}

.stats-info {
  position: relative;
  padding: 4px;

  display: flex;
  flex-flow: column nowrap;
}

.progress-bar {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background-color: var(--stats-progress-background);
  border-radius: 0 0 var(--border-radius) var(--border-radius);
  z-index: -1;
}

.stats-seen-failed {
  font-size: 10pt;
}
</style>

<template>
  <div class="stats-item">
    <span class="stats-word">{{ item.word }}</span>

    <div class="stats-info">
      <div class="progress-bar" :style="{ width: `${retention}%`}"></div>
      <span class="stats-retention">{{ retention }}%</span>
      <span class="stats-seen-failed">F:{{ item.failed }} S:{{ item.seen }}</span>
    </div>
  </div>
</template>

<script>
export default {
  name: "StatsItem",
  props: {
    item: Object,
  },
  data() {
    return {
      retention: 100,
    }
  },
  watch: {
    item: {
      deep: true,
      immediate: false,
      handler(v) {
        this.retention =
          100 - Math.round(this.item.failed / this.item.seen * 100);
      }
    }
  }
}
</script>
