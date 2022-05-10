<style>
@import url('https://fonts.googleapis.com/css2?family=Radio+Canada:wght@300;400;500;600;700&display=swap');

@import url("./colors.css");

@keyframes failed-shake {
  0%   { transform: translateX(0); }
  33%  { transform: translateX(-4px); }
  66%  { transform: translateX(4px); }
  100% { transform: translateX(0); }
}

::-webkit-scrollbar,
::-webkit-scrollbar-corner
{
  --size: 12px;
  width: var(--size);
  height: var(--size);
  background-color: var(--scrollbar-background);
}

::-webkit-scrollbar-thumb {
  background-color: var(--scrollbar-thumb-background);
}

* {
  box-sizing: border-box;
}

[hidden] {
  display: none !important;
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
  align-items: center;
  gap: 22px;
}

#input-wrapper {
  width: 100%;
  display: flex;
  flex-flow: column nowrap;
}

input {
  background-color: var(--input-background);
  color: var(--input-text);
  border: solid 1px var(--input-border);
  font: inherit;
  font-weight: 800;
  text-align: center;
  outline: none;
  border-radius: 0 0 var(--border-radius) var(--border-radius);
  padding: 8px;
  transition: 200ms;
}

input:hover {
  background-color: var(--input-background-hover);
  color: var(--input-text-hover);
  border-left-color: var(--input-border-hover);
  border-right-color: var(--input-border-hover);
  border-left-width: var(--border-radius);
  border-right-width: var(--border-radius);
}

input:focus {
  background-color: var(--input-background-focus);
  color: var(--input-text-focus);
  border-left-color: var(--input-border-focus);
  border-right-color: var(--input-border-focus);
  border-left-width: var(--border-radius);
  border-right-width: var(--border-radius);
}

input.failed {
  background-color: var(--input-failed-background);
  color: var(--input-failed-text);
  border-color: var(--input-failed-border);

  animation: 200ms failed-shake forwards linear;
}
</style>

<template>
  <Definitions :definitions="word.english" />

  <div id="input-wrapper">
    <WordType :type="word.type" />

    <input type="text"
      placeholder="type here"
      autofocus
      v-model="input"
      :class="{ failed: isFailed }"
      :answers="word.toki"
      @keydown="checkWord">
  </div>

  <Answers :answers="answers" />

  <WordStats :stats="stats"
    :is-invisible="isStatsInvisible"/>
</template>

<script>
import WordStats from "./components/WordStats.vue";
import WordType from "./components/WordType.vue";
import Definitions from "./components/Definitions.vue";
import Answers from "./components/Answers.vue";

import dictionary from "./assets/dictionary.json";

// all toki pona words
const wordsToki = dictionary
  .map((word)=> word.toki)
  .flat()
  .sort()

// remove duplicates
for (let i = wordsToki.length - 1; i >= 0; i--)
  if (wordsToki[i] === wordsToki[i+1])
    wordsToki.splice(i+1, 1);

const wordStats = wordsToki.map((word)=> ({
  word: word,
  seen: 0,
  failed: 0,
}));

// TODO
// ADD STATS (POPOUT)
// - MASTERY % GRAPH
// - KEY PRESSES

export default {
  name: "App",
  components: {
    WordStats,
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
      stats: wordStats,
      isStatsInvisible: true,
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

    window.addEventListener("keydown", (keyboardEvent)=> {
      if (keyboardEvent.key == "Tab") {
        keyboardEvent.preventDefault();

        if (!keyboardEvent.repeat)
          this.isStatsInvisible = false;
      }
    });

    window.addEventListener("keyup", (keyboardEvent)=> {
      if (keyboardEvent.key == "Tab") {
        this.isStatsInvisible = true;
      }
    });
  }
}
</script>
