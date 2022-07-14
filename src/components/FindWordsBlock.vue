<script>
import FindWordsString from "./FindWordsString.vue";

// TODO: генерация мусора по принципу согласная-гласная
// TODO: Добавление символа впереди в отдельную функцию (если вообще оно надо)
import { words as wordsAll } from "../assets/russian-simple-nouns";

// TODO: вынести все настройки в отдельный файл
const WORDS_MIN = 1; // мин количество слов в строке
const WORDS_MAX = 4; // макс количество слов в строке

export default {
  name: "FindWordsBlock",
  data() {
    return {
      wordsQuantity: this.getRandomInt(WORDS_MIN, WORDS_MAX),
      words: [],
    };
  },
  components: {
    FindWordsString,
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
      let wordIndex = this.getRandomInt(0, wordsAll.length - 1);
      return wordsAll.splice(wordIndex, 1)[0];
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
    checkSelection() {
      this.$refs.FindWordsString.check();
    },
  },
};
</script>

<template>
  <main>
    <FindWordsString :words="words" ref="FindWordsString" />

    <button @click="checkSelection" class="button is-info">
      <span class="icon"> <i class="fa-solid fa-clipboard-check"></i> </span>
      <span>Проверить</span>
    </button>
  </main>
</template>

<style lang="scss"></style>
