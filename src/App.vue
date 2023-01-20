<script>
import { modifierKeys, whitespaceKeys } from './keys';
export default {
  data() {
    return {
      letterToType: 'a',
      input: '',
      isHighlighted: false,
      history: [],
      historyItem: { letter: 'a', tries: 0 },
      charOptions: []
    }
  },
  computed: {
    historyLenght() {
      return this.history.length;
    }
  },
  methods: {
    getRandomChar() {
      return this.charOptions[Math.floor(Math.random() * this.charOptions.length)];
    },
    toggleFocus() {
      this.isHighlighted = !this.isHighlighted;
    },
    handleKeypress(e) {
      console.group('keypress');
      console.log('keypress', e);
      console.log('keypress code', e.which);
      console.log('keypress key', e.key);
      console.groupEnd();
      if (this.charOptions.includes(e.key)) {
        this.historyItem.tries++;
      }
      if (this.letterToType === e.key) {
        // const parsedHistoryItem = {
        //   ...this.historyItem,
        //   tries: Math.ceil(this.historyItem.tries / 2)
        // };
        this.history.push(this.historyItem);

        this.letterToType = this.getRandomChar();
        this.historyItem = { letter: this.letterToType, tries: 0 }
        this.input = '';
      }
    }
  },
  created() {
    this.charOptions = new Array(126).fill(0).map((_, i) => i).filter((charCode) => charCode >= ' '.charCodeAt(0)).map((charCode) => String.fromCharCode(charCode)).concat(modifierKeys).concat(whitespaceKeys);
    console.log(this.charOptions);
  },
  mounted() {
    window.addEventListener('keydown', this.handleKeypress);
  }
}
</script>

<template>
  <div>
    <div class='letter-row'>
      <div class="letterDubble letter">a</div>
      <h1 class='letter'>{{ letterToType }}</h1>
    </div>
    <input class='char-input' v-model='input' @input='' @focus='toggleFocus' @blur='toggleFocus'
      :class='{ highlighted: isHighlighted }' type="text" @change='newLetter'>
  </div>
  <div class='container'>
    <b>history</b>{{ " | " }}<span>Chars typed: {{ historyLenght }}</span>
    <ul>
      <li v-for='charItem in  history'>{{ charItem.letter }} <span>attemps: {{ charItem.tries }}</span></li>
    </ul>
  </div>

</template>

<style scoped>
.char-input {
  margin: 20px;
  border-left: none;
  border-right: none;
  border-top: none;
  border-bottom: 0.5px solid black;
}

.container {
  overflow: auto;
}

.letter-row {
  position: relative;
  justify-content: center;
  align-items: center;
  width: 100px;
  height: 100px;
  display: flex;
  margin: auto;
}

.letter {
  display: absolute;
  position: absolute;
  font-size: 3.2em;
  margin: auto;
}

.letterDubble {
  color: blueviolet;
  /* top: 90px; */
}

.letterDubbleActive {
  color: red;
  animation: move-and-fade 1s ease-in-out;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

@keyframes move-and-fade {
  0% {
    opacity: 1;
    transform: translateX(0);
  }

  100% {
    opacity: 0;
    transform: translateX(-100px);
  }
}

ul>* {
  border: 0 0 2px 0 solid salmon;
}

.highlighted {
  border-bottom: 2px solid lightskyblue;
}

.char-input:focus {
  outline: 0;
  /* border-bottom: 1px solid blue; */
}
</style>
