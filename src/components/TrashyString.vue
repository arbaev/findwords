<script>
const STR_LEN = 40; // длина строки

export default {
  name: "TrashyString",
  props: ["words"],
  data() {
    return {
      str: "",
      wordsRanges: [],
      selections: [],
    };
  },
  mounted() {
    this.str = this.strConstructor(this.words);
    this.wordsRanges = this.setRanges(this.words);
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

      const span = document.createElement("span");
      span.textContent = selectionText;
      span.className = "highlight_text";

      let range = selection.getRangeAt(0);
      this.selections.push([selectionText, range]);
      document.getSelection().removeAllRanges(); // очистить текущее выделение, если оно существует
      range.deleteContents();
      range.insertNode(span);
    },
    resetSelection() {
      this.$refs.strNode.innerText = this.str;
      this.selections = [];
    },

    wordsUnion() {
      let wordsArray = [...this.wordsRanges];

      const checkSelections = this.selections.map((wr) => {
        const [word, range] = wr;

        let findy = wordsArray.filter;

        if (wordsArray.includes(word)) {
          const index = wordsArray.indexOf(word);
          wordsArray.splice(index, 1);
        }
        return wr;
      });

      wordsArray.forEach((word) => {
        //  найти и установить к слову range
        const start = this.str.indexOf(word);
        const end = start + word.length;
        console.log(`from ${start} to ${end}`);
        this.$refs.strNode.innerText = this.str;
        const node = this.$refs.strNode;
        let range = new Range();
        range.setStart(node.firstChild, start);
        range.setEnd(node.firstChild, end);
        console.log(range);
      });
    },

    setRanges(words) {
      // устанавливает диапазоны словам
      return words.map((word) => {
        this.$refs.strNode.innerText = this.str; // хак с установкой кастомного поля
        const nodeRef = this.$refs.strNode;

        const start = this.str.indexOf(word);
        const end = start + word.length;

        let range = new Range();
        range.setStart(nodeRef.firstChild, start);
        range.setEnd(nodeRef.firstChild, end);

        return [word, range];
      });
    },

    check() {
      if (this.selections.length === 0) return true;
      console.info("check");

      // this.wordsUnion();
      // let wds = Array.from(new Set([...this.words, ...this.selections]));
      // console.log(wds);

      let result = this.selections.forEach((item) => {
        const [word, range] = item;
        if (this.words.includes(word)) {
          this.highlightRange(word, range, "green");
        } else {
          this.highlightRange(word, range, "red");
        }
        this.removeRange(item);
      });

      this.wordsRanges.forEach((item) => {
        const [word, range] = item;
        this.highlightRange(word, range, "orange");
      });

      // let result = this.selections.reduce((acc, item) => {
      //   const [word, range] = item;
      //   if (this.words.includes(word)) {
      //     this.highlightRange(word, range, "green");
      //   } else {
      //     this.highlightRange(word, range, "red");
      //   }

      //   // console.log(word);
      //   // console.log(range);
      //   return acc;
      // }, this.str);

      console.log(result);
      // this.words.forEach((w) => {
      //   if (this.selections.includes(w)) {
      //     this.highlight(w, "green");
      //   } else {
      //     this.highlight(w, "red");
      //   }
      // });
    },

    removeRange(item) {
      // удаляет указанный объект item из массива объектов this.wordsRanges
      const indexOfObject = this.wordsRanges.findIndex((i) => i[0] === item[0]);
      if (indexOfObject > -1) {
        this.wordsRanges.splice(indexOfObject, 1);
      }
    },

    highlightRange(word, range, color) {
      const span = document.createElement("span");
      span.textContent = word;
      span.className = "highlight_" + color;

      range.deleteContents();
      range.insertNode(span);
    },

    highlight(word, color) {
      const node = this.$refs.strNode;
      var text = node.childNodes[0];
      // node.innerText = this.str;
      const textNode = node.innerText;

      const start = textNode.indexOf(word);
      const end = start + word.length;
      console.log(`from ${start} to ${end}`);

      const span = document.createElement("span");
      span.textContent = word;
      span.className = "highlight_" + color;

      node.insertBefore(span, text.splitText(start));

      // console.log(txt2);
      // node.deleteContents();
      // node.insertNode(span);
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
  background: lightblue;
}
.highlight_text {
  background-color: lightblue;
}
.highlight_green {
  background-color: greenyellow;
}
.highlight_red {
  background-color: crimson;
}
.highlight_orange {
  background-color: orange;
}
</style>
