<style scoped>
.word {
  flex-grow: 1;
  background-color: var(--word-background);
  border: solid 1px var(--word-border);
  border-radius: var(--border-radius);
  padding: 6px 12px;
}
</style>

<template>
  <span class="word">
    {{ text }}
  </span>
</template>

<script>
export default {
  name: "TextItem",
  props: {
    text: String,
  },
}
</script>
