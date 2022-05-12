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
  font: inherit;
  font-weight: 800;
  text-align: center;
  outline: none;
  border: solid 1px var(--input-border);
  border-top: none;
  border-radius: 0 0 var(--border-radius) var(--border-radius);
  padding: 8px;
  transition: 200ms;
}

input:hover {
  background-color: var(--input-background-hover);
  color: var(--input-text-hover);
  border-left-color: var(--input-border-hover);
  border-right-color: var(--input-border-hover);
  border-left-width: calc(var(--border-radius) + 1px);
  border-right-width: calc(var(--border-radius) + 1px);
}

input:focus {
  background-color: var(--input-background-focus);
  color: var(--input-text-focus);
  border-left-color: var(--input-border-focus);
  border-right-color: var(--input-border-focus);
  border-left-width: calc(var(--border-radius) + 1px);
  border-right-width: calc(var(--border-radius) + 1px);

}

input.failed {
  background-color: var(--input-background-failed);
  color: var(--input-text-failed);
  border-left-color: var(--input-border-failed);
  border-right-color: var(--input-border-failed);
  animation: 200ms failed-shake forwards linear;
}

#shameless {
  position: fixed;
  bottom: 10px;
  right: 16px;
  font-size: 10pt;
  color: #FFFF63 !important;
  text-decoration: none;
  opacity: .4;
  transition: 400ms;
}

#shameless:hover {
  opacity: 1;
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

  <WordStats :batches="batches"
    :is-invisible="isStatsInvisible"
    @rowStateChange="toggleRowState" />

  <a id="shameless"
    href="https://ko-fi.com/hosma"
    title="thanks :)">ko-fi</a>
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

const batchSize = 5;
const batches = [];

for (let i = 0; i < wordStats.length / batchSize; i++) {
  const batch = wordStats.slice(
    i*batchSize, (i*batchSize)+batchSize)

  batches.push({
    items: batch,
    active: false
  });
}

batches[0].active = true;

const randomInArray =(array)=> array[Math.floor(Math.random() * array.length)];

// TODO
// FINISH WORD BATHCES

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
      possibleWords: [],
      word: {},
      input: "",
      answers: [],
      isFailed: false,

      batches: batches,
      stats: wordStats,
      isStatsInvisible: true,
    }
  },
  mounted() {
    this.rowStates = new Array(this.batches.length)
      .fill(false)

    this.toggleRowState(0, true);
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
  },
  methods: {
    randomWord() {
      const wordToki = randomInArray(this.possibleWords);
      // console.log(wordToki);

      return dictionary.find((entry)=> entry.toki.includes(wordToki));
    },
    newWord() {
      this.isFailed = "";
      this.answers = [];
      this.word = this.randomWord();
      // console.log(this.word.toki);

      this.stats.find((item)=> this.word.toki.includes(item.word))
        .seen += 1
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

      this.stats.find((item)=> this.word.toki.includes(item.word))
        .failed += 1
    },
    toggleRowState(index, newState) {
      this.batches[index].active = newState;

      if (!this.batches.filter((batch)=> batch.active).length)
        this.batches[index].active = !newState;

      // console.table(this.batches.map((batch)=> batch.active));

      this.possibleWords = this.batches
        .filter((batch)=> batch.active)
        .map((batch)=> batch.items)
        .flat()
        .map((item)=> item.word)
    }
  },
}
</script>
