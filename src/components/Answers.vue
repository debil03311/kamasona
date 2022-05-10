<style scoped>
#answers {
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  gap: 6px 12px;
}

.word {
  background-color: var(--word-answers-background);
  border-color: var(--word-answers-border);
}
</style>

<template>
  <div id="answers">
    <TextItem v-for="answer of answers"
      :key="answer"
      :text="answer" />
  </div>
</template>

<script>
import TextItem from "./TextItem.vue";

export default {
  name: "Answers",
  components: {
    TextItem,
  },
  props: {
    answers: Array,
  },
}
</script>
