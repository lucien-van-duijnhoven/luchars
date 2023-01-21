<script>
import { modifierKeys, nonCharKeys } from './keys';
import Temp from './components/Temp.vue';
export default {
  components: {
    Temp,
  },
  data() {
    return {
      letterToType: 'a',
      input: '',
      inputHistory: [],
      inputBuffer: new Map(),
      inputBufferCount: 0,
      isHighlighted: false,
      history: [],
      historyItem: { letter: 'a', tries: 0 },
      charOptions: [],
      mode: 'alpha'
    }
  },
  watch: {
    mode() {
      this.createCharOptions();
      console.log(this.charOptions);
    },
  },
  computed: {
    historyLenght() {
      return this.history.length;
    },
    inputHistoryArray() {
      return Array.from(this.inputBuffer, ([key, value]) => ({ key, value }));
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
        this.inputHistory.push(e.key);
      }
      if (this.letterToType === e.key) {
        // const parsedHistoryItem = {
        //   ...this.historyItem,
        //   tries: Math.ceil(this.historyItem.tries / 2)
        // };
        this.history.push(this.historyItem);
        this.inputBuffer.set(this.inputBufferCount, e.key);
        this.inputBufferCount++;

        this.letterToType = this.getRandomChar();
        this.historyItem = { letter: this.letterToType, tries: 0 }
        this.input = '';
      }
    },
    createCharOptions() {
      if (this.mode === 'alpha') {
        this.charOptions = new Array(26).fill(0).map((_, i) => i).map((number) => String.fromCharCode(number + 'a'.charCodeAt(0)));
      } else if (this.mode === 'full') {
        this.charOptions = new Array(126).fill(0).map((_, i) => i).filter((charCode) => charCode >= ' '.charCodeAt(0)).map((charCode) => String.fromCharCode(charCode)).concat(modifierKeys).concat(nonCharKeys);
      } else if (this.mode === 'uppercased') {
        this.charOptions = new Array(26).fill(0).map((_, i) => i).reduce((acc, number) => [...acc, String.fromCharCode(number + 'A'.charCodeAt(0)), String.fromCharCode(number + 'a'.charCodeAt(0))], [])
      } else if (this.mode === 'keys') {
        this.charOptions = new Array(126).fill(0).map((_, i) => i).filter((charCode) => charCode >= ' '.charCodeAt(0)).map((charCode) => String.fromCharCode(charCode)).concat(nonCharKeys);
      }
    },
    handleTempEnd(index) {
      console.log('handleTempEnd', index);
      this.inputBuffer.delete(index);
      console.log('Input history', this.inputBuffer);
    },
  },
  created() {
    this.createCharOptions();
    console.log(this.charOptions);
  },
  mounted() {
    window.addEventListener('keydown', this.handleKeypress);
  }
}
</script>

<template>
  <!-- <Temp class="letterDubble letter" v-for="({ key, value }) in inputHistoryArray" :key="key" :timer="1000" :index="key"
    @end="handleTempEnd">{{
      value
    }}</Temp> -->
  <div>
    <label for="">mode:
      <select name="" v-model="mode" id="">
        <option value="alpha">alpha</option>
        <option value="full">full</option>
        <option value="keys">keys</option>
        <option value="uppercased">uppercased</option>
      </select> </label>
    <div class='letter-row'>
      <Temp class="letterDubble letter" v-for="({ key, value }) in inputHistoryArray" :key="key" :timer="1000"
        :index="key" @end="handleTempEnd">{{
          value
        }}</Temp>
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
  nonCharKeys
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
  animation: move-and-fade 1s ease-in-out;
  animation-iteration-count: 1;
  animation-fill-mode: forwards;
}

.letterDubbleActive {
  color: red;
  animation: move-and-fade 1s ease-in-out;
  animation-iteration-count: infinite;
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
