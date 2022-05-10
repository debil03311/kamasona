<style scoped>
#definitions {
  width: 100%;

  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  gap: 6px 12px;
}
</style>

<template>
  <div id="definitions">
    <TextItem v-for="definition of definitions"
      :key="definition"
      :text="definition" />
  </div>
</template>

<script>
import TextItem from "./TextItem.vue";

export default {
  name: "Definitions",
  components: {
    TextItem,
  },
  props: {
    definitions: Array,
  },
}
</script>
