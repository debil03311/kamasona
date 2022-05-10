<style scoped>
#word-type {
  color: var(--type-text);
  font-weight: 800;
  font-size: 16pt;
  text-transform: uppercase;
}

#word-type.noun {
  color: var(--type-noun-text);
}

#word-type.verb {
  color: var(--type-verb-text);
}

#word-type.pre-verb {
  color: var(--type-pre-verb-text);
}

#word-type.adjective {
  color: var(--type-adjective-text);
}

#word-type.preposition {
  color: var(--type-preposition-text);
}

#word-type.particle {
  color: var(--type-particle-text);
}

#word-type.number {
  color: var(--type-number-text);
}
</style>

<template>
  <span id="word-type"
    :class="type">
    {{ type }}
  </span>
</template>

<script>
export default {
  name: "WordType",
  props: {
    type: String,
  }
}
</script>
