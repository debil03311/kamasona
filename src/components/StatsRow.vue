<style scoped>
.stats-row {
  cursor: pointer;

  display: grid;
  grid-template-columns: repeat(5, 1fr);
  gap: 1em;
}
</style>

<template>
  <div class="stats-row"
    :class="{ active: state }"
    @click="toggleActive">

    <StatsItem v-for="item of batch"
      :key="item"
      :item="item" />
  </div>
</template>

<script>
import StatsItem from "./StatsItem.vue";

export default {
  name: "StatsRow",
  components: {
    StatsItem
  },
  props: {
    batch: Array,
    state: Boolean,
    rowIndex: Number,
  },
  methods: {
    toggleActive() {
      this.$emit("rowStateChange", this.rowIndex, !this.state);
    }
  },
}
</script>
