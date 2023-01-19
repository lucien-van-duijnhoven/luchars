<script>
export default {
  data() {
    return {
      letterToType: 'a',
      input: '',
      isHighlighted: false,
      history: [],
      historyItem: { letter: 'a', tries: 0 }
    }
  },
  watch: {
    input() {
      console.log(this.input);
      const charCodeLastInput = this.input.charCodeAt(this.input.length - 1);
      if ((charCodeLastInput > 'a'.charCodeAt(0) && charCodeLastInput < 'z'.charCodeAt())) {
        this.historyItem.tries++;
        console.log("iterate", this.historyItem);
      }
      if (this.letterToType === this.input) {
        // const parsedHistoryItem = {
        //   ...this.historyItem,
        //   tries: Math.ceil(this.historyItem.tries / 2)
        // };
        this.history.push(this.historyItem);

        this.letterToType = this.getRandomChar();
        this.historyItem = { letter: this.letterToType, tries: 0 }
        this.input = '';
      }
    },
  },
  computed: {
    historyLenght() {
      return this.history.length;
    }
  },
  methods: {
    getRandomChar() {
      const min = 'a'.charCodeAt(0);
      const max = 'z'.charCodeAt(0);
      let randomNum = Math.floor(Math.random() * (max - min + 1) + min);
      return String.fromCharCode(randomNum);
    },
    toggleFocus() {
      this.isHighlighted = !this.isHighlighted;
    },
    handleKeypress(e) {
      console.log(e);
      console.log(e.data);
      if (e.data && (e.data.charCodeAt(0) >= 'a'.charCodeAt(0) && e.data.charCodeAt(0) <= 'z'.charCodeAt(0))) {
        console.log('valid');
      }
    }
  },
}
</script>

<template>
  <div>
    <div class='letter-row'>
      <h1 class=''>{{ letterToType }}</h1>
    </div>
    <input class='char-input' v-model='input' @input='handleKeypress' @focus='toggleFocus' @blur='toggleFocus'
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
