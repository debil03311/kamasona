<style>
@import url('https://fonts.googleapis.com/css2?family=Radio+Canada:wght@300;400;500;600;700&display=swap');

@import url("./colors.css");

@keyframes failed-shake {
  0%   { transform: translateX(0); }
  33%  { transform: translateX(-4px); }
  66%  { transform: translateX(4px); }
  100% { transform: translateX(0); }
}

* {
  box-sizing: border-box;
}

body {
  min-height: 100vh;
  background-color: var(--body-background);
  color: var(--text);
  font: 400 14pt "Radio Canada", sans-serif;
  text-align: center;
  padding: 1em;
  margin: 0;

  display: grid;
  place-items: center;

  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
}

#app {
  --border-radius: 8px;
  width: clamp(256px, 90%, 400px);

  display: flex;
  flex-flow: column nowrap;
  gap: 22px;
}

input,
.word
{
  border: solid 1px;
  border-radius: var(--border-radius);
  padding: 6px 12px;
}

input {
  background-color: var(--input-background);
  color: var(--input-text);
  border-color: var(--input-border);
  font: inherit;
  font-weight: 800;
  text-align: center;
  outline: none;
  transition: 200ms;
}

input:hover {
  background-color: var(--input-background-hover);
  color: var(--input-text-hover);
  border-color: var(--input-border-hover);
}

input:focus {
  background-color: var(--input-background-focus);
  color: var(--input-text-focus);
  border-color: var(--input-border-focus);
}

input.failed {
  background-color: var(--input-failed-background);
  color: var(--input-failed-text);
  border-color: var(--input-failed-border);

  animation: 200ms failed-shake forwards linear;
}
</style>

<template>
  <WordType :type="word.type" />
  <Definitions :definitions="word.english" />

  <input type="text"
    placeholder="type here"
    v-model="input"
    :class="{ failed: isFailed }"
    :answers="word.toki"
    @keydown="checkWord">

  <Answers :answers="answers" />
</template>

<script>
import WordType from "./components/WordType.vue";
import Definitions from "./components/Definitions.vue";
import Answers from "./components/Answers.vue";

import dictionary from "./assets/dictionary.json";

// all toki pona words
const wordsToki = dictionary.map((word)=> word.toki)
  .flat().sort()

// remove duplicates
for (let i = wordsToki.length - 1; i >= 0; i--)
  if (wordsToki[i] === wordsToki[i+1])
    wordsToki.splice(i+1, 1);

// TODO
// ADD STATS (POPOUT)
// - MASTERY % GRAPH
// - KEY PRESSES

export default {
  name: "App",
  components: {
    WordType,
    Definitions,
    Answers,
  },
  data() {
    return {
      word: {},
      input: "",
      answers: [],
      isFailed: false,
    }
  },
  methods: {
    randomWord() {
      return dictionary[Math.floor(Math.random() * dictionary.length)];
    },
    newWord() {
      this.isFailed = "";
      this.answers = [];
      this.word = this.randomWord();
    },
    async checkWord(keyboardEvent) {
      await new Promise((resolve)=> setTimeout(resolve));

      if (keyboardEvent.key === "Enter")
        return this.failWord();

      if (this.word.toki.includes(this.input)) {
        this.input = "";
        return this.newWord();
      }
    },
    failWord() {
      this.answers = this.word.toki;
      this.isFailed = true;
    }
  },
  mounted() {
    this.newWord();
    // this.answers = this.word.toki;
  }
}
</script>
