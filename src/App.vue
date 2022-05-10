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

#answers,
#definitions
{
  display: flex;
  flex-flow: row wrap;
  justify-content: center;
  gap: 6px 12px;
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

.word {
  flex-grow: 1;
  background-color: var(--word-background);
  border-color: var(--word-border);
}

#answers .word {
  background-color: var(--word-answers-background);
  border-color: var(--word-answers-border);
}

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
    :class="word.type">
    {{ word.type }}
  </span>

  <div id="definitions">
    <span class="word"
      v-for="definition of word.english"
      :key="definition">
      {{ definition }}
    </span>
  </div>

  <input type="text"
    v-model="input"
    :answers="word.toki"
    @keydown="checkWord"
    :class="{ failed: isFailed }">

  <div id="answers">
    <div class="word"
      v-for="word of answers"
      :key="word">
      {{ word }}
    </div>
  </div>
</template>

<script>
import dictionary from "./assets/dictionary.json";

export default {
  name: "App",
  data() {
    return {
      word: {},
      input: "",
      answers: ["acidic"],
      isFailed: false,
    }
  },
  methods: {
    newWord() {
      this.isFailed = "";
      this.answers = "";

      this.word = dictionary[Math.floor(Math.random() * dictionary.length)];
      console.log(this.word.toki);
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
