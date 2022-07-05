<script>
// TODO: генерация мусора по принципу согласная-гласная
// TODO: Добавление символа впереди в отдельную функцию (если вообще оно надо)
import { words } from "./assets/russian-simple-nouns";

const STR_LEN = 40; // длина строки
const WORDS_MIN = 1; // мин количество слов в строке
const WORDS_MAX = 4; // макс количество слов в строке

export default {
  name: "FindWords",
  data() {
    return {
      str: "bla",
      wordsQuantity: this.getRandomInt(WORDS_MIN, WORDS_MAX),
      ws: [],
    };
  },
  components: {},
  created() {
    this.ws = this.getRandWordsArray();
    this.str = this.strConstructor(this.ws);
  },
  mounted() {},
  methods: {
    getRandomInt(min, max) {
      // Получение случайного целого числа в заданном интервале
      min = Math.ceil(min);
      max = Math.floor(max);
      return Math.floor(Math.random() * (max - min + 1)) + min;
    },

    spliceRandWord() {
      // возвращает случайное слово из массива слов, удаляя его в исходном массиве
      let wordIndex = this.getRandomInt(0, words.length - 1);
      return words.splice(wordIndex, 1)[0];
    },

    addFirstLetter(arr) {
      // добавляет первую случайную букву к каждому элементу массива
      return arr.map((item) => this.randomString(1) + item);
    },

    getRandWordsArray() {
      // возвращает массив выбранных слов
      let arr = [];

      for (let k = 0; k < this.wordsQuantity; k++) {
        arr.push(this.spliceRandWord()); //.toUpperCase());
      }
      return this.addFirstLetter(arr);
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
  <main>
    <h2>{{ str }}</h2>
  </main>
</template>

<style>
h2 {
  text-align: center;
}
</style>
