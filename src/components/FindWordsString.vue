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
    // TODO: после нажатия Проверить — дизаблить сброс выделения
    // TODO: запускать таймер в начале игры и в конце выводить коэф внимательности и скорости
    mouseSelect(event) {
      let selection = document.getSelection();
      let selectionText = selection.toString();

      if (event.detail > 1) return false; // исключаем влияение двойного и тройного клика
      if (selectionText === "") return false; // исключаем клик без выделения
      if (selectionText.trim() === this.str) return false; // исключаем выделение всей строки
      if (!this.str.includes(selectionText.trim())) return false; // исключаем выделение других строк или элементов

      const span = document.createElement("span");
      span.textContent = selectionText;
      span.className = "highlight_text";

      let range = selection.getRangeAt(0);
      this.selections.push([selectionText, range]);
      document.getSelection().removeAllRanges(); // очистить текущее выделение, если оно существует
      range.deleteContents();
      range.insertNode(span);
    },

    // TODO: корректно удалять выделение при селекшене нескольких строк или других элементов
    resetSelection() {
      this.$refs.strNode.innerText = this.str;
      this.selections = [];
    },

    check() {
      // запуск проверки действий пользователя, вызов при клике на кнопку
      if (this.selections.length === 0) return false;

      this.stringHightlight();
    },

    stringHightlight() {
      // расставляет по строке span с классами для подсветки слов
      // названия классов:
      // highlight_correct для правильно выделенных слов
      // highlight_wrong для неправильно выделенных слов
      // highlight_omitted для пропущенных и невыделенных слов
      const node = this.$refs.strNode;
      let range = new Range();
      range.selectNodeContents(node);
      const children = range.commonAncestorContainer.children;

      let wordsArray = [...this.words];

      // сначала обрабатываем селекты юзера
      for (let element of children) {
        if (wordsArray.includes(element.innerText)) {
          element.className = "highlight_correct";

          const index = wordsArray.indexOf(element.innerText);
          wordsArray.splice(index, 1);
        } else {
          element.className = "highlight_wrong";
        }
      }

      // затем обрабатываем пропущенные юзером слова
      wordsArray.forEach((word) => {
        const span = document.createElement("span");
        span.textContent = word;
        span.className = "highlight_omitted";

        node.innerHTML = node.innerHTML.replace(word, span.outerHTML);
      });
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

    strConstructor(words) {
      // конструктор строки со словами и мусорными буквами
      let wordsArray = [...words];
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
      return this.arrToString(wordsArrayRandom);
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
  <section class="">
    <div class="columns is-centered">
      <div class="column is-narrow">
        <p
          @mouseup="mouseSelect($event)"
          ref="strNode"
          class="title is-family-monospace is-size-3 has-text-centered"
        >
          {{ str }}
        </p>
      </div>
      <div class="column is-1">
        <button @click="resetSelection" class="button is-white">
          <span class="icon">
            <i class="fa-solid fa-xmark delete-button"></i>
          </span>
        </button>
      </div>
    </div>
    select: {{ selections }}
  </section>
</template>

<style lang="scss">
h2::selection {
  background: lightblue;
}
.highlight_text {
  background-color: lightblue;
}
.highlight_correct {
  background-color: greenyellow;
}
.highlight_wrong {
  background-color: crimson;
}
.highlight_omitted {
  background-color: orange;
}
.delete-button {
  color: crimson;
}
</style>
