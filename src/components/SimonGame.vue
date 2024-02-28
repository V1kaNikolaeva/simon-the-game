<template>
  <div class="simon-game">
    <div class="contanier">
      <button
        v-for="panel in panels"
        :key="panel.id"
        :class="[panel.class, panel.active ? 'active' : null]"
        v-bind:disabled="isButtonDisabled"
        @click="handler(panel.id)"
      ></button>
    </div>
    <div v-for="item in modes" :key="item.id" class="pickMode">
      <label for="">{{ item.name }}</label>
      <input type="radio" :value="item.mode" v-model="currentMode" />
    </div>
    <UiButton @click="startGame" buttonType="default">Начать игру</UiButton>
    <div v-if="defeat">
        <UiMessage>Вы проиграли!</UiMessage>
    </div>
    <div class="wins-counter">
        <p>Победы: {{ wins }}</p>
    </div>
  </div>
</template>

<script>
import UiButton from "./UiButton.vue";
import UiMessage from "./UiMessage.vue";

export default {
  name: "SimonGame",
  components: { UiButton, UiMessage },
  data() {
    return {
      panels: [
        { id: 1, class: "simon_button blue", active: false },
        { id: 2, class: "simon_button red", active: false },
        { id: 3, class: "simon_button yellow", active: false },
        { id: 4, class: "simon_button green", active: false },
      ],

      modes: [
        { id: 1, name: "Легкая сложность", mode: "easy" },
        { id: 2, name: "Нормальная сложность", mode: "normal" },
        { id: 3, name: "Сложная сложность", mode: "hard" },
      ],

      sounds: [
        { id: 1, sound: new Audio(require("../sounds/sounds_1.mp3")) },
        { id: 2, sound: new Audio(require("../sounds/sounds_2.mp3")) },
        { id: 3, sound: new Audio(require("../sounds/sounds_3.mp3")) },
        { id: 4, sound: new Audio(require("../sounds/sounds_4.mp3")) },
      ],

      audioPlay: null,

      currentClickPanel: 0,
      currentUserClicks: [],
      panelsId: [1, 2, 3, 4],

      currentMode: "easy",
      currentGame: [],
      currentGameLength: 5,

      defeat: false,
      wins: 0,
      isButtonDisabled: false,
    };
  },

  methods: {
    //Метод обработки нажатия на плиту
    handler(panel) {
      this.currentClickPanel = panel;
      let audio = this.sounds.find((item) => item.id === this.currentClickPanel);

      //Передаем нажатую плиту чтобы она "загорелась" и время на которая она "загорится"
      this.lightOn(this.currentClickPanel, 500);

      //В currentUserClicks передаем нажатую плиту
      this.currentUserClicks.push(this.currentClickPanel);

      audio.sound.currentTime = 0;
      audio.sound.play();

      this.endGame();

    },

    startGame() {
      this.defeat = false;
      if (this.currentMode) {
        switch (this.currentMode) {
          case "easy":
            this.easyMode();
            this.audioPlay.clearTimeout()
            break;
          case "normal":
            this.normalMode();
            this.audioPlay.clearTimeout()
            break;
          case "hard":
            this.hardMode();
            this.audioPlay.clearTimeout()
            break;
        }
      } else {
        alert("Выберите сложность!");
      }
    },

    //Метод запустит все нужные проверки на победную комбинацию
    endGame() {
      //Если метод сравнения вернет true, то прибавляем победу
      if (this.compare()) {
        this.wins += 1;
        this.defeat = false;
        this.currentGame = [];
        this.currentUserClicks = [];
        //Переходим к следующему раунду
        setTimeout(() => {
          this.startGame();
        }, 2000)
        //Иначе сравниваем длину массива и что compare вернул false,
        //чтобы предотвратить обнуление массивов
      } else if (this.currentGame.length === this.currentUserClicks.length && !this.compare()) {
        this.defeat = true;
        this.currentGame = [];
        this.currentUserClicks = [];
        this.currentGameLength = Math.random() * (5 - 1) + 1;
      }
    },

    easyMode() {
      this.currentGame = [];
      this.fillCurrentGame(this.currentGameLength);
      this.playAudio(this.currentGame, 1500);
    },

    normalMode() {
      this.currentGame = [];
      this.fillCurrentGame(this.currentGameLength);
      this.playAudio(this.currentGame, 1000);
    },

    hardMode() {
      this.currentGame = [];
      this.fillCurrentGame(this.currentGameLength);
      this.playAudio(this.currentGame, 400);
    },

    playAudio(currentGame, time) {
      //Запрещаем нажимать при проигрывании комбинации на кнопки
      this.isButtonDisabled = true;
      //Очищаем currentUserClicks если вдруг были клики
      this.currentUserClicks = [];
      if (currentGame.length > 0) {
        let audio = this.sounds.find((item) => currentGame.at(0) === item.id);
        audio.sound.currentTime = 0;
        audio.sound.play();
        this.lightOn(audio.id, time)
        this.audioPlay = setTimeout(() => {
          this.playAudio(currentGame.slice(1), time);
        }, time);
      } else {
        this.isButtonDisabled = false;
      }
    },

    //Метод заполняет currentGame числами, которые находятся в panelsId
    //При этом учитывается длина currentGame
    fillCurrentGame(currentGameLength) {
      for (let i = 0; i < currentGameLength; i++) {
        let randomIndex = Math.floor(Math.random() * this.panelsId.length);
        this.currentGame.push(this.panelsId[randomIndex]);
      }
      console.log(this.currentGame);
    },

    lightOn(id, time) {
        let lightPanel = this.panels.find((item) => item.id === id)
        lightPanel.active = true;
        setTimeout(() => {
            lightPanel.active = false;
        }, time)
    },

    compare() {
      return JSON.stringify(this.currentGame) === JSON.stringify(this.currentUserClicks)
    },
  },

  watch: {
    //Если игрок победил, то количество плиток увеличивается
    wins() {
      this.currentGameLength += 1;
    },
  },

  mounted() {
    //Присваиваем currentGameLength нчальное значени (1 - 5 плиток)
    this.currentGameLength = Math.floor(Math.random() * (5 - 1) + 1);
  }
};
</script>

<style scoped>

.simon-game {
    margin: 0px 30px 0px 30px;
}
.contanier {
  display: grid;
  justify-content: center;
  grid-template-columns: auto auto;
  grid-gap: 0px;
}

.pickMode {
    display: flex;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
    height: 50px;
    margin: 30px 0px 30px 0px;
}


input {
    border: 0px;
    height: 20px;
    width: 20px;
}

input:hover {
    cursor: pointer;
}

button {
  border: none;
}

button:hover {
  cursor: pointer;
}

.wins-counter {
    position: fixed;
    bottom: 50px;
    right: 50px;
}

.wins-counter > p {
    color: #89e687;
}

.blue,
.red,
.yellow,
.green {
  width: 100px;
  height: 100px;
}

.blue {
  border-radius: 90px 0px 0px 0px;
  background-color: #a6e9f5;
}
.red {
  border-radius: 0px 90px 0px 0px;
  background-color: #ea8585;
}
.yellow {
  border-radius: 0px 0px 0px 90px;
  background-color: #e6e37a;
}
.green {
  border-radius: 0px 0px 90px 0px;
  background-color: #89e687;
}

.blue.active {
  background-color: #256396;
  outline: 2px solid #256396;
}
.red.active {
  background-color: #962525;
  outline: 2px solid #962525;
}
.yellow.active {
  background-color: #b3d12d;
  outline: 2px solid #b3d12d;
}
.green.active {
  background-color: #279625;
  outline: 2px solid #279625;
}
</style>
