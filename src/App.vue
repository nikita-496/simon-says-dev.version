<template>
  <div id="app">
    <h1 class="simon-tag">Simon Says</h1>
    <div id="appColorButtons">
      <button class="colorButton red" id="red" @click="handleColorButton"></button>
      <button class="colorButton green" id="green" @click="handleColorButton"></button>
      <button class="colorButton blue" id="blue" @click="handleColorButton"></button>
      <button class="colorButton yellow" id="yellow" @click="handleColorButton"></button>
    </div>
    <div id="counter">
      <h2>Counter</h2>
      <div id="counterDisplay">
        <span>{{ count }}</span>
      </div>
    </div>
    <div id="startStrict">
      <button class="commandButton" id="start" @click="start">START</button>
    </div>
    <div class="info">
      <h3>Level</h3>
      <input type="radio" value="1500" v-model="time" :checked="true" /> Easy
      <input type="radio" value="1000" v-model="time" /> Normal
      <input type="radio" value="400" v-model="time" /> Hard
    </div>
  </div>
</template>

<script>
  export default {
    data() {
      return {
        count: "--",
        step: 0,
        time: "1500",
        restart: false,
        playerTurn: false, //флаг, устанавливающий ход игрока
        //последовательности программы
        cpuSequence: [],
        //последовательности игрока
        playerSequence: [],
      };
    },
    methods: {
      //Обработка события нажатия кнопки старт
      start: function () {
        this.count = 0;
        this.step = 0;
        this.cpuSequence = [];
        this.playerSequence = [];
        this.addCpuStep();
        this.playCpuSequence(this.cpuSequence);
      },

      //Программа гернерирует случайную последовательность
      playCpuSequence: function (sequence) {
        if (this.cpuSequence.length === 20) {
          this.playerTurn = false;
          this.count = "WIN";
        } else {
          for (let i = 0; i < sequence.length; i++) {
            //задержка между последовательностями
            setTimeout(() => {
              this.audioSignal(sequence[i]);
              this.lightColorButton(sequence[i]);
            }, 300 + i * this.time);
          }
          this.playerTurn = true;
          setTimeout(() => {
            this.playerTurn = true;
          }, 350 + sequence.length * 1000);
        }
      },

      //Игрок нажал на кнопку (цветной кргу)
      // => обработка события нажатия на цветной круг
      handleColorButton: function (event) {
        if (this.playerTurn) {
          //воспроизвелся звук соответ. кнопки
          this.audioSignal(event.target.id);
          //загорелась кнопка
          this.lightColorButton(event.target.id);
          //в массив последовательности игрока добавлена текущая кнопка
          this.playerSequence.push(event.target.id);
          //сравниваем последовательности выданные программой и игроком
          if (this.compareSequence(this.step)) {
            debugger;
            this.step += 1;
            //игра длится до 20 последовательностей и их совпажений между программой и игроком
            if (this.step >= this.cpuSequence.length && this.cpuSequence.length < 20) {
              debugger;
              //ход переходет программе
              this.playerTurn = false;
              setTimeout(() => {
                this.addCpuStep();
                this.playCpuSequence(this.cpuSequence);
                this.playerSequence = [];
                this.step = 0;
              }, 1250);
            }
          }
          //последовательности не равны
          //=> поражение (игра окончена)
          else {
            this.playerTurn = false;
            alert(`Sorry, you lost after ${this.count} rounds!`);
          }
        }
      },

      //Сравнение последовательности программы и игрока
      compareSequence: function (step) {
        let cpu = this.cpuSequence.slice();
        let player = this.playerSequence.slice();
        return cpu[step] === player[step];
      },

      //Программа произвольно генерирует последовательность
      addCpuStep: function () {
        switch (Math.floor(Math.random() * 4)) {
          case 0:
            this.cpuSequence.push("red");
            break;
          case 1:
            this.cpuSequence.push("green");
            break;
          case 2:
            this.cpuSequence.push("blue");
            break;
          case 3:
            this.cpuSequence.push("yellow");
            break;
        }
        this.count += 1;
      },

      //Воспроизведение звука, соответсвующей кнопки
      audioSignal: function (color) {
        const redAudio = new Audio(require("../public/sounds/sounds_1.mp3"));
        const greenAudio = new Audio(require("../public/sounds/sounds_2.mp3"));
        const blueAudio = new Audio(require("../public/sounds/sounds_3.mp3"));
        const yellowAudio = new Audio(require("../public/sounds/sounds_4.mp3"));

        switch (color) {
          case "red":
            redAudio.play();
            break;
          case "green":
            greenAudio.play();
            break;
          case "blue":
            blueAudio.play();
            break;
          case "yellow":
            yellowAudio.play();
            break;
        }
      },

      //Освещение целевой (актуальной) кнопки
      lightColorButton: function (color) {
        document.getElementById(color).classList.remove(color);
        document.getElementById(color).classList.add(color + "-active");
        setTimeout(() => {
          document.getElementById(color).classList.remove(color + "-active");
          document.getElementById(color).classList.add(color);
        }, 700);
      },
    },
  };
</script>

<style>
  @import "./css/main.css";
</style>
