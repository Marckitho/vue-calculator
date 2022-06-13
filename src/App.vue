<script>
import { toNumber } from '@vue/shared'

export default {
  data() {
    return {
      numberList: [
        1, 2, 3, 4, 5, 6, 7, 8, 9, 0, ".", "="
      ],

      operatorList: [
        "AC", "÷", "×", "-", "+"
      ],

      firstTerm: "",
      operator: "",
      secondTerm: "",
      displayText: "",

      volume: 5,
      toggleVolume: true
    }
  },

  methods: {
    clear() {
      this.firstTerm = ""
      this.operator = ""
      this.secondTerm = ""
    },
    
    numberClick(number) {
      switch(number) {
        case "=":
          if (this.secondTerm != "") this.calculate()
          break
        case ".":
          if(this.operator == "") { // Primeiro termo
            if (this.firstTerm.length < 1) this.firstTerm = "0."
            else {
              this.firstTerm = this.firstTerm.replace(".", "");
              this.firstTerm += "."
            }
          } else { // Segundo termo
            if (this.secondTerm.length < 1) this.secondTerm = "0."
            else {
              this.secondTerm = this.secondTerm.replace(".", "");
              this.secondTerm += "."
            }
          }
          break
        case 0:
          if(this.operator == "") {
            if (this.firstTerm.length > 0) this.firstTerm += number
          } else {
            if (this.secondTerm.length > 0) this.secondTerm += number
          }
          break
        default:
          if (this.operator == "") this.firstTerm += number
          else this.secondTerm += number
      }
    },

    updateDisplay() {
      this.displayText = this.firstTerm;
      if (this.operator) this.displayText += ` ${this.operator}`
      if (this.secondTerm) this.displayText += ` ${this.secondTerm}`
    },

    operatorClick(operator) {
      if(operator == "AC") {
        this.clear()
      } else {
        if (this.firstTerm != "") {
          if (this.secondTerm == "") this.operator = operator
          else {
            this.calculate()
            this.operator = operator
          }
        }
        
      }
    },

    calculate() {
      var result = 0
      var a = toNumber(this.firstTerm)
      var b = toNumber(this.secondTerm)

      switch(this.operator) {
        case "÷":
          result = a / b
          break
        case "×":
          result = a * b
          break
        case "-":
          result = a - b
          break
        case "+":
          result = a + b
          break
      }

      // Atualizar campos
      this.firstTerm = result
      this.operator = ""
      this.secondTerm = ""
    }
  },

  watch: {
    firstTerm() {
      this.updateDisplay()
    },

    secondTerm() {
      this.updateDisplay()
    },

    operator() {
      this.updateDisplay()
    }
  }
}
</script>

<template>
  <div class="volume">
    <div class="volume-icon" @click="toggleVolume = !toggleVolume">
      <ion-icon v-if="!toggleVolume" name="volume-mute" size="large"></ion-icon>
      <ion-icon v-else-if="volume > 7" name="volume-high" size="large"></ion-icon>
      <ion-icon v-else-if="volume > 4" name="volume-medium" size="large"></ion-icon>
      <ion-icon v-else-if="volume > 1" name="volume-low" size="large"></ion-icon>
      <ion-icon v-else name="volume-off" size="large"></ion-icon>
    </div>
    <div class="volume-slider">
      <input type="range" min="0" max="10" v-model="volume" @change="toggleVolume = true">
    </div>
  </div>
  <div class="calculator">
    <input disabled type="text" class="display" placeholder="0" v-model="displayText">
    <div class="buttons">
      <div class="numbers">
        <button v-for="number in numberList" class="number" @click="numberClick(number)">{{ number }}</button>
      </div>
      <div class="operators">
        <button v-for="operator in operatorList" class="operator" @click="operatorClick(operator)">{{ operator }}</button>
      </div>
    </div>
  </div>
</template>

<style>
/* Fontes */
@import url('https://fonts.googleapis.com/css2?family=Noto+Serif:ital,wght@0,400;0,700;1,400;1,700&display=swap');
@font-face {
font-family: "Calculator";
src: url("././assets/fonts/calculator.ttf");
src: url("././assets/fonts/calculator-webfont.woff") format("woff"),
}


/* Variáveis da Paleta */
:root {
  --bg: #37E2D5;

  --main: #FBCB0A;
  --main-dark: #C7A108;

  --number: #C70A80;
  --number-dark: #93075F;

  --operator: #590696;
  --operator-dark: #3B0463;
}

* {
  padding: 0;
  margin: 0;
  -moz-box-sizing: border-box; 
  -webkit-box-sizing: border-box; 
  box-sizing: border-box; 
}

#app {
  width: 100vw;
  height: 100vh;

  background-color: var(--bg);

  display: flex;
  justify-content: center;
  align-items: center;
}

/* Controlador de Volume */
.volume {
  position: fixed;
  top: 0;
  right: 0;

  display: flex;
  flex-direction: column;

  align-items: center;
  gap: 10px;

  margin-top: 20px;
  margin-right: 20px;
}

.volume-icon {
  width: min-content;
  padding-block: 8px;

  color: var(--operator-dark);

  cursor: pointer;
  z-index: 2;
}

.volume-slider, .volume-icon {
  background: var(--main);

  padding-inline: 12px;
  border-radius: 5px;

  box-shadow: 0px 5px 0px var(--main-dark);
}

.volume-slider input[type=range] {
  height: 25px;
  -webkit-appearance: none;
  margin: 10px 0;
  width: 100%;
  background: none;
}

.volume-slider input[type=range]:focus {
  outline: none;
}

.volume-slider input[type=range]::-webkit-slider-runnable-track {
  width: 100%;
  height: 5px;
  cursor: pointer;
  box-shadow: 0px 0px 0px #000000;
  background: var(--operator);
  border-radius: 3px;
  border: 0px solid #000000;
}

.volume-slider input[type=range]::-webkit-slider-thumb {
  box-shadow: 0px 0px 0px #000000;
  outline: 2px solid var(--main);
  height: 16px;
  width: 16px;
  border-radius: 25px;
  background: var(--operator-dark);
  cursor: pointer;
  -webkit-appearance: none;
  margin-top: -6px;
}

.volume-slider input[type=range]:focus::-webkit-slider-runnable-track {
  background: var(--operator-dark);
}

.volume-slider {
  transition: all .2s;
  transition-delay: .4s;

  opacity: 0;
  transform: translateY(-30px);
}

.volume:hover .volume-slider {
  opacity: 1;
  transform: translateY(0px);
}

/* Calculadora */

.calculator {
  border-radius: 20px;

  background-color: var(--main);

  width: 300px;
  height: 440px;

  display: grid;
  grid-template-rows: 1fr 7fr;
  row-gap: 20px;

  padding: 24px 14px;

  box-shadow: 0px 30px 0px var(--main-dark);
}

.display {
  border-radius: 10px;
  
  border: none;
  color: black;
  background-color: #1bc653;

  box-shadow: 0px -5px var(--main-dark);

  margin-top: 5px;
  font-family: 'Calculator';
  font-size: xx-large;
  text-align: end;

  width: 100%;

  padding: 5px 10px;

  text-overflow: ellipsis;
}

.buttons {
  display: grid;
  grid-template-columns: 4fr 2fr;
  column-gap: 10px;
}

button {
  border: none;
  font-size: xx-large;

  border-radius: 2px;

  cursor: pointer;
  user-select: none;
}

.numbers {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-template-rows: repeat(4, 1fr);
  row-gap: 30px;
  column-gap: 10px;
}

.number {
  background-color: var(--number);

  display: flex;
  align-items: center;
  justify-content: center;

  box-shadow: 0 10px var(--number-dark);
  transition-duration: 50ms;
}

.number:active {
  box-shadow: 0 2px var(--number-dark);
  transform: translateY(8px);
}

.operators {
  display: grid;
  grid-template-rows: repeat(4, 1fr) 3fr;
  grid-template-columns: 1fr;
  row-gap: 15px;

  padding: 0 10px;
}

.operator {
  background-color: var(--operator);
  color: aliceblue;

  box-shadow: 0 10px var(--operator-dark);
  transition-duration: 50ms;
}

.operator:active {
  box-shadow: 0 2px var(--operator-dark);
  transform: translateY(8px);
}
</style>
