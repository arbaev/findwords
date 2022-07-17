<script>
import FindWordsString from "./FindWordsString.vue";

// TODO: генерация мусора по принципу согласная-гласная
// TODO: Добавление символа впереди в отдельную функцию (если вообще оно надо)
// TODO: вынести все настройки в отдельный файл
const STRINGS_MAX = 5; // макс количество слов в строке

export default {
  name: "FindWordsBlock",
  data() {
    return {
      stringsMax: STRINGS_MAX,
    };
  },
  components: {
    FindWordsString,
  },
  methods: {
    // getRandomInt(min, max) {
    //   // Получение случайного целого числа в заданном интервале
    //   min = Math.ceil(min);
    //   max = Math.floor(max);
    //   return Math.floor(Math.random() * (max - min + 1)) + min;
    // },
    // addFirstLetter(arr) {
    //   // добавляет первую случайную букву к каждому элементу массива
    //   return arr.map((item) => this.randomString(1) + item);
    // },
  },
};
</script>

<template>
  <main>
    <template v-for="index in stringsMax" :key="index">
      <FindWordsString />
    </template>

    <button @click="checkSelection" class="button is-info">
      <span class="icon"> <i class="fa-solid fa-clipboard-check"></i> </span>
      <span>Проверить</span>
    </button>
  </main>
</template>

<style lang="scss"></style>
