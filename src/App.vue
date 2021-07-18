<template>
  <div id="app">
    <div class="game-area">
      <button v-bind:disabled="areaLock" @click="playerTurn(0)"></button>
      <button v-bind:disabled="areaLock" @click="playerTurn(1)"></button>
      <button v-bind:disabled="areaLock" @click="playerTurn(2)"></button>
      <button v-bind:disabled="areaLock" @click="playerTurn(3)"></button>
    </div>
    <div class="menu">
      <h1>Simon Game</h1>
      <h2>Menu</h2>
      <select v-model="difficulty">
        <option value="1500">Легкий</option>
        <option value="1000">Средний</option>
        <option value="400">Сложный</option>
      </select>
      <button v-on:click="gameStart()" v-bind:disabled="gameStarted">
        Start
      </button>
      <button v-if="gameStarted" v-on:click="gameEnd('restart')">Стоп</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      difficulty: 1500,
      areaLock: true,
      gameStarted: false,
      gameArea: document.querySelector(".game-area"),
      buttonArray: [],
      player: 0,
      audio: null,
      sounds: [
        "https://s3.amazonaws.com/freecodecamp/simonSound1.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound2.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound3.mp3",
        "https://s3.amazonaws.com/freecodecamp/simonSound4.mp3",
      ],
    };
  },
  methods: {
    gameStart() {
      this.audio = new Audio();
      this.gameArea = document.querySelector(".game-area").childNodes;
      this.gameStarted = !this.gameStarted;
      this.generateButtons();
    },
    gameEnd(type) {
      if (type === "gameLose") {
        alert("Вы проиграли!");
      }
      this.gameArea.forEach((el) => el.classList.remove("active"));
      this.gameStarted = !this.gameStarted;
      this.buttonArray = [];
      this.areaLock = true;
      this.player = 0;
    },
    playSound(id) {
      this.audio.src = this.sounds[id];
      this.audio.play();
    },
    generateButtons() {
      this.areaLock = true;
      this.player = 0;
      const buttonValue = Math.floor(Math.random() * 4);
      if (this.buttonArray[this.buttonArray.length - 1] !== buttonValue) {
        this.buttonArray.push(buttonValue);
        this.checkButtons(0, buttonValue);
      } else {
        this.generateButtons();
      }
    },
    async checkButtons(num, id) {
      if (num !== this.buttonArray.length) {
        this.gameArea[this.buttonArray[num]].classList.add("active");

        setTimeout(() => {
          if (this.gameStarted) {
            this.gameArea[this.buttonArray[num]].classList.remove("active");
            this.checkButtons(num + 1);
          }
        }, Number(this.difficulty));
      } else {
        this.areaLock = false;
      }
    },
    playerTurn(val) {
      this.playSound(val);
      if (val !== this.buttonArray[this.player]) {
        this.gameEnd("gameLose");
      } else {
        this.player = this.player + 1;
        if (this.player === this.buttonArray.length) {
          this.generateButtons();
        }
      }
    },
  },
};
</script>

<style lang="scss">
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@500&display=swap");

* {
  font-family: "Montserrat", sans-serif;
}

body {
  margin: 0;
  padding: 0;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  background: rgb(154, 58, 180);
  background: linear-gradient(
    90deg,
    rgba(154, 58, 180, 1) 15%,
    rgba(252, 69, 85, 1) 77%
  );
}

#app {
  height: 40vh;
  width: 50vw;
  display: flex;
  background: white;
}

.game-area {
  width: 60%;
  display: flex;
  flex-wrap: wrap;

  > button {
    border: none;
    height: 50%;
    width: 50%;
    transition: all 0.3s;

    &:active {
      background: black !important;
    }

    &.active {
      background: white !important;
    }
  }

  > button:nth-child(1) {
    background: purple;
  }
  > button:nth-child(2) {
    background: darkslateblue;
  }
  > button:nth-child(3) {
    background: palevioletred;
  }
  > button:nth-child(4) {
    background: rebeccapurple;
  }
}

.menu {
  height: 80%;
  width: 40%;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: space-evenly;
}

button {
  border: 1px solid black;
  height: 24px;
  width: 48px;
  background: white;
  transition: all 0.5s;
  &:hover {
    cursor: pointer;
    background: black;
    color: white;
  }
}
</style>
