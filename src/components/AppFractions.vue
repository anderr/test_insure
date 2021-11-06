<template lang="html">
  <div>
    <div class="fractions">
      <div class="fractions__head">
        Вычисляем дроби. Почему бы и нет?
      </div>

      <BaseButton text="Добавить дробь" @is-clicked="addFraction(1)" :addClasses="isDisabledClass" />

      <div class="fractions__main">
        <div class="fractions__items">

          <div class="fractions__item" v-for="(item, index) of numbers" :key="index">
            <div class="fractions__fraction">
              <input
                type="number"
                class="fractions__input"
                name=""
                value=""
                v-model.number="item.one"
                min="1"
                max="99">
              <div class="fractions__item-line"></div>
              <input
                type="number"
                class="fractions__input"
                name=""
                value=""
                v-model.number="item.two">

              <BaseButton
                text="Удалить"
                add-classes="btn-red btn-small"
                class="fractions__delete"
                v-if="numbers.length > 2"
                @is-clicked="removeFraction(index)"/>
            </div>
            <div class="fractions__sign">
              +
            </div>

          </div>

        </div>

        <div class="fractions__sign">
          =
        </div>
        <div class="fractions__result">
          <div class="fractions__result-fraction">
            <div class="fractions__result-number">{{ result.one }}</div>
            <div class="fractions__item-line"></div>
            <div class="fractions__result-number">{{ result.two }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import BaseButton from '../components/BaseButton.vue';

export default {
  data() {
    return {
      numbers: [],
      form: {},
    }
  },
  beforeMount() {
    // Первоначально генерируем 2 дроби
    this.addFraction(2);
  },
  components: {
    BaseButton
  },
  methods: {
    checkNumber(event, value) {
      let block = event.target.getAttribute('data-input');

      if (value > 99) {
        document.querySelector('[data-input="' + block + '"]').value = 99;
      }
      if (value < 0) {
        document.querySelector('[data-input="' + block + '"]').value = 1;
      }
    },
    addFraction(iterations) {
      // такое вот ограничение через аксессоры объекта, зато работает :)
      let object = {
        _one: 1,
        _two: 2,
        get one() {
          return this._one;
        },
        set one(value) {
          this._one = value < 1 ? 1 : value > 99 ? 99 : value;
        },
        get two() {
          return this._two;
        },
        set two(value) {
          this._two = value < 1 ? 1 : value > 99 ? 99 : value;
        },
      }

      for (var i = 0; i < iterations; i++) {
        let second = this.getRandomInt(1, 99);
        let first = this.getRandomInt(1, second);

        let newObj = Object.create(object);
        newObj.one = first;
        newObj.two = second;

        this.numbers.push(newObj);
      }

      return false;
    },
    removeFraction(index) {
      this.numbers.splice(index, 1);
    },
    getRandomInt(min, max) {
      min = Math.ceil(min);
      max = Math.floor(max);

      return Math.floor(Math.random() * (max - min + 1)) + min;
    },
    makeSumm(numbers) {
      let denoms = [];
      let result = [];

      for (const key of Object.keys(numbers)) {
        denoms.push(numbers[key].two);
      }

      // Идем по минимальному сопротивлению, если знаменатели равны, то делаем простое сложение числителей.
      if (this.isEqual(denoms)) {

        result = this.makeSummSameDenominator(numbers);
      } else {
        // Если знаменатели разные, то ищем наименьшее общее кратное
        const Nok = this.nok(denoms);
        let extraMultiplier = [];

        // Собираем множители в том же порядке для каждого из объектов дроби
        for (const key of Object.keys(numbers)) {
          let newMultiplier = Nok / numbers[key].two;

          extraMultiplier.push(newMultiplier);
        }

        // умножаем каждую дробь на множитель каждой дроби, получаем массив объектов дробей с общим знаменателем и далее складываем как обычно
        for (let i = 0; i < extraMultiplier.length; i++) {
          let multiplier = extraMultiplier[i];

          let newNumerator = numbers[i].one * multiplier;
          let newDenominator = numbers[i].two * multiplier;

          result[i] = { one: newNumerator, two: newDenominator };
        }

        result = this.makeSummSameDenominator(result);
      }

      return result;
    },
    makeSummSameDenominator(numbers) {
      let numeratorSumm = 0;

      for (const key of Object.keys(numbers)) {
        numeratorSumm += parseInt(numbers[key].one);
      }
      if (numeratorSumm === numbers[0].two) {
        return {one: 1, two: 1};
      } else {
        return {one: numeratorSumm, two: numbers[0].two};
      }
    },
    isEqual(array) {
      let equal = true;

      for (let i = 0; i < array.length; i++) {
        let firstVal = parseInt(array[i]);

        for (let n = 0; n < array.length; n++) {
          if (n !== i) {
            let secondVal = parseInt(array[n]);

            equal = (firstVal === secondVal)

            if (!equal) return false;
          }
        }
      }

      return equal;
    },
    nok(A) {
      var  n = A.length, a = Math.abs(A[0]);
      for (var i = 1; i < n; i++) {
        var b = Math.abs( A[i] ), c = a;
        while (a && b) {
          a > b ? a %= b : b %= a;
        }
        a = Math.abs( c * A[i]) / (a + b);
      }

      return a;
    }
  },
  computed: {
    result() {
      return this.makeSumm(this.numbers);
    },
    isDisabledClass() {
      if (this.numbers.length >= 5) {
        return 'disabled';
      } else {
        return '';
      }
    }
  }
}
</script>

<style lang="scss" scoped>
.fractions {
  max-width: 1000px;

  &__main {
    display: flex;
    justify-content: center;
    align-items: center;
  }
  &__head {
    font-size: 1.2em;
    margin-bottom: 30px;
  }
  &__items {
    display: flex;
    flex-direction: row;
  }
  &__item {
    display: flex;
    flex-direction: row;
    position: relative;

    &:last-child {
      .fractions__sign {
        display: none;
      }
    }
  }
  &__fraction {
    display: flex;
    flex-direction: column;
    margin: 0 5px;
    position: relative;
  }
  &__delete {
    position: absolute;
    bottom: -70px;
    left: 50%;
    transform: translateX(-50%);
  }
  &__sign {
    font-size: 1.5em;
    color: #555;
    margin-left: 10px;
    margin-right: 10px;
    display: flex;
    justify-content: center;
    align-items: center;
  }
  &__input {
    height: 40px;
    line-height: 40px;
    color: #555;
    text-align: center;
    width: 60px;
    font-size: 1.2em;

    &:focus {
      outline: none;
    }
  }
  &__item-line {
    margin: 7px auto;
    width: 60px;
    height: 1px;
    background: #555;
  }

  &__result {
    font-size: 1.4em;
    display: flex;
    flex-direction: row;
  }
}
</style>
