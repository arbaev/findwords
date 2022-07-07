<script>
const STR_LEN = 40; // длина строки

export default {
  name: "TrashyString",
  props: ["words"],
  data() {
    return {
      str: "",
      selections: [],
    };
  },
  created() {
    this.str = this.strConstructor(this.words);
  },
  methods: {
    mouseSelect(event) {
      if (event.detail > 1) {
        // исключаем влияение двойного и тройного клика
        event.preventDefault();
        return false;
      }

      let selection = document.getSelection();
      let selectionText = selection.toString();

      if (selectionText === "") return false; // исключаем клик без выделения
      if (selectionText === this.str) return false; // исключаем выделение всей строки

      this.selections.push(selectionText);

      const span = document.createElement("span");
      span.textContent = selectionText;
      span.className = "highlight_text";

      let range = selection.getRangeAt(0);
      document.getSelection().removeAllRanges(); // очистить текущее выделение, если оно существует
      range.deleteContents();
      range.insertNode(span);
    },
    resetSelection() {
      this.$refs.strNode.innerText = this.str;
      this.selections = [];
    },
    // TODO: выделить в хелпер?
    getRandomInt(min, max) {
      // Получение случайного целого числа в заданном интервале
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },

    randomString(n) {
      // генерирует строку случайных символов (заглавная кириллица) длиной n
      let str = "";
      while (str.length < n)
        str += String.fromCharCode(Math.random() * 1106).replace(
          /[^А-ЯЁ]|_/g,
          ""
        );
      return str;
    },

    howManyLetters(arr) {
      // суммирует количество букв в массиве строк
      return arr.reduce((acc, i) => (acc += i.length), 0);
    },

    strConstructor(wordsArray) {
      // дополняет массив слов мусорными элементами и перемешивает его
      while (this.howManyLetters(wordsArray) < STR_LEN - 3) {
        // заполняем массив рандомом на 3 меньше нужной длины
        let n = this.getRandomInt(1, 3);
        wordsArray.push(this.randomString(n));
      }
      // дополняем массив до требуемой длины (эта суета нужна, чтобы не вылезти за пределы STR_LEN)
      let count = this.howManyLetters(wordsArray);
      if (count < STR_LEN) {
        wordsArray.push(this.randomString(STR_LEN - count));
      }

      const wordsArrayRandom = this.shuffle(wordsArray);
      const ws = this.arrToString(wordsArrayRandom);
      return ws;
    },

    shuffle(arr) {
      // перемешивает элементы массива случайным образом
      let j, temp;
      for (let i = arr.length - 1; i > 0; i--) {
        j = Math.floor(Math.random() * (i + 1));
        temp = arr[j];
        arr[j] = arr[i];
        arr[i] = temp;
      }
      return arr;
    },

    arrToString(arr) {
      // конвертирует массив строк в строку
      return arr.reduce((acc, item) => (acc = acc + item), "");
    },
  },
};
</script>
<template>
  <h2 @mouseup="mouseSelect($event)" ref="strNode">{{ str }}</h2>
  <input type="button" value="reset" @click="resetSelection" />
  select: {{ selections }}
</template>
<style lang="scss">
h2::selection {
  background: yellowgreen;
}
.highlight_text {
  background-color: yellowgreen;
}
</style>
