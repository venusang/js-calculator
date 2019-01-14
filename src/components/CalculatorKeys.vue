<template>
  <div class="calculator__keys">
    <button class="key--operator" data-action="add">+</button>
    <button class="key--operator" data-action="subtract">-</button>
    <button class="key--operator" data-action="multiply">&times;</button>
    <button class="key--operator" data-action="divide">÷</button>
    <button>7</button>
    <button>8</button>
    <button>9</button>
    <button>4</button>
    <button>5</button>
    <button>6</button>
    <button>1</button>
    <button>2</button>
    <button>3</button>
    <button>0</button>
    <button data-action="decimal">.</button>
    <button data-action="clear">AC</button>
    <button class="key--equal" data-action="calculate">=</button>
  </div>
</template>

<script>
import Vue from 'vue';
import axios from 'axios';
import VueAxios from 'vue-axios';

Vue.use(VueAxios, axios);
/* eslint-disable */
export default {
  name: 'CalculatorKeys',
  methods: {
    calculatorActions: ()=> {
      const calculate = (n1, operator, n2) => {
      const firstNum = parseFloat(n1);
      const secondNum = parseFloat(n2);
      let apiConnected;


      Vue.axios.get('/api/v1/calculate', {
        params: {
          firstNum: firstNum,
          secondNum: secondNum,
          operator: operator
          }
        })
        .then((response) => {
          return response.data.result;
        })
        .catch((error) => {
          apiConnected = false;
        }
      );

      if(!apiConnected) {
        if (operator === 'add') return firstNum + secondNum;
        if (operator === 'subtract') return firstNum - secondNum;
        if (operator === 'multiply') return firstNum * secondNum;
        if (operator === 'divide') return firstNum / secondNum;
      }
    }

    const getKeyType = key => {
      const { action } = key.dataset;
      if (!action) return 'number'
      if (
        action === 'add' ||
        action === 'subtract' ||
        action === 'multiply' ||
        action === 'divide'
      ) return 'operator';

      return action;
    }

    const createResultString = (key, displayedNum, state) => {
      const keyContent = key.textContent;
      const keyType = getKeyType(key);
      const {
        firstValue,
        operator,
        modValue,
        previousKeyType
      } = state

      if (keyType === 'number') {
        return displayedNum === '0' ||
          previousKeyType === 'operator' ||
          previousKeyType === 'calculate'
          ? keyContent
          : displayedNum + keyContent;
      }

      if (keyType === 'decimal') {
        if (!displayedNum.includes('.')) return displayedNum + '.'
        if (previousKeyType === 'operator' || previousKeyType === 'calculate') return '0.'
        return displayedNum;
      }

      if (keyType === 'operator') {
        return firstValue &&
          operator &&
          previousKeyType !== 'operator' &&
          previousKeyType !== 'calculate'
          ? calculate(firstValue, operator, displayedNum)
          : displayedNum;
      }

      if (keyType === 'clear') return 0;

      if (keyType === 'calculate') {
        return firstValue
          ? previousKeyType === 'calculate'
            ? calculate(displayedNum, operator, modValue)
            : calculate(firstValue, operator, displayedNum)
          : displayedNum;
      }
    }

    const updateCalculatorState = (key, calculator, calculatedValue, displayedNum) => {
      const keyType = getKeyType(key)
      const {
        firstValue,
        operator,
        modValue,
        previousKeyType
      } = calculator.dataset;

      calculator.dataset.previousKeyType = keyType;

      if (keyType === 'operator') {
        calculator.dataset.operator = key.dataset.action;
        calculator.dataset.firstValue = firstValue &&
          operator &&
          previousKeyType !== 'operator' &&
          previousKeyType !== 'calculate'
          ? calculatedValue
          : displayedNum;
      }

      if (keyType === 'calculate') {
        calculator.dataset.modValue = firstValue && previousKeyType === 'calculate'
          ? modValue
          : displayedNum;
      }

      if (keyType === 'clear' && key.textContent === 'AC') {
        calculator.dataset.firstValue = '';
        calculator.dataset.modValue = '';
        calculator.dataset.operator = '';
        calculator.dataset.previousKeyType = '';
      }
    }

    const updateVisualState = (key, calculator) => {
      const keyType = getKeyType(key);
      Array.from(key.parentNode.children).forEach(k => k.classList.remove('is-depressed'));

      if (keyType === 'operator') key.classList.add('is-depressed');
      if (keyType === 'clear' && key.textContent !== 'AC') key.textContent = 'AC';
      if (keyType !== 'clear') {
        const clearButton = calculator.querySelector('[data-action=clear]')
        clearButton.textContent = 'CE';
      }
    }

    const calculator = document.querySelector('.calculator');
    const display = calculator.querySelector('.calculator__display');
    const keys = calculator.querySelector('.calculator__keys');

    keys.addEventListener('click', e => {
      if (!e.target.matches('button')) return
      const key = e.target;
      const displayedNum = display.textContent;
      const resultString = createResultString(key, displayedNum, calculator.dataset);

      display.textContent = resultString;
      updateCalculatorState(key, calculator, resultString, displayedNum);
      updateVisualState(key, calculator);
    });

    }
  },
  mounted() {
    this.calculatorActions();
  }
}
</script>

<style>
</style>
