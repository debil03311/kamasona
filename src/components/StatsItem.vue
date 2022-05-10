<style scoped>
.stats-item {
  --padding: 6px;
  position: relative;
  border: solid 1px var(--stats-item-border);
  border-radius: var(--border-radius);
  padding-bottom: var(--padding);

  display: flex;
  flex-flow: column nowrap;
  gap: var(--padding);
}

.progress-bar {
  position: absolute;
  top: 0;
  left: 0;
  height: 100%;
  background-color: var(--stats-progress-background);
  border-radius: var(--border-radius);
  z-index: -1;
}

.stats-word {
  background-color: var(--stats-item-border);
  font-weight: 600;
  border-radius: var(--border-radius) var(--border-radius) 0 0;
  padding: 4px;
}

.stats-seen-failed {
  font-size: 10pt;
}
</style>

<template>
  <div class="stats-item">
    <div class="progress-bar" :style="{ width: `${retention}%`}"></div>
    <span class="stats-word">{{ item.word }}</span>
    <span class="stats-retention">{{ retention }}%</span>
    <span class="stats-seen-failed">{{ item.failed }}/{{ item.seen }}</span>
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
      // percentage
      retention: Math.round(this.item.failed / this.item.seen * 100),
    }
  },
  beforeMount() {
    this.item.seen = Math.floor(Math.random() * 64);
    this.item.failed = Math.floor(Math.random() * this.item.seen);
    this.retention = Math.round(this.item.failed / this.item.seen * 100);
  }
}
</script>
