<style scoped>
.word {
  flex-grow: 1;
  background-color: var(--word-background);
  border-color: var(--word-border);
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
