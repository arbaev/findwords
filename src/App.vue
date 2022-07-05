<script>
// TODO: генерация мусора по принципу согласная-гласная
// TODO: Добавление символа впереди в отдельную функцию (если вообще оно надо)
import { words } from "./assets/russian-simple-nouns";
import TrashyString from "./components/TrashyString.vue";

// TODO: вынести все настройки в отдельный файл
const WORDS_MIN = 1; // мин количество слов в строке
const WORDS_MAX = 4; // макс количество слов в строке

export default {
  name: "FindWords",
  data() {
    return {
      wordsQuantity: this.getRandomInt(WORDS_MIN, WORDS_MAX),
      words: [],
    };
  },
  components: {
    TrashyString,
  },
  created() {
    this.words = this.getRandWordsArray();
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

    // addFirstLetter(arr) {
    //   // добавляет первую случайную букву к каждому элементу массива
    //   return arr.map((item) => this.randomString(1) + item);
    // },

    getRandWordsArray() {
      // возвращает массив выбранных слов
      let arr = [];

      for (let k = 0; k < this.wordsQuantity; k++) {
        arr.push(this.spliceRandWord()); //.toUpperCase());
      }
      return arr; // this.addFirstLetter(arr);
    },
  },
};
</script>

<template>
  <main>
    <trashy-string :words="words"></trashy-string>
  </main>
</template>

<style>
h2 {
  text-align: center;
}
</style>
