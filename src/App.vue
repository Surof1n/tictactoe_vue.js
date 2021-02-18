<template>
  <div id="app">
    <div class="container__inputs">
      <label for="label1">Игрок 1 (X)</label>
      <input
        id="label1"
        type="text"
        v-bind:value="gamerNameFirst"
        v-on:input="inputChangeHandlerFirst"
        maxlength="10"
      />

      <label for="label2">Игрок 2 (O)</label>
      <input
        id="label2"
        type="text"
        v-bind:value="gamerNameSecond"
        v-on:input="inputChangeHandlerSecond"
        maxlength="10"
      />
    </div>
    <div class="game__container">
      <p
        class="lower"
        v-if="
          (gameActiveName == 'Напишите имена' ||
            gameActiveName == 'Одинаковые имена') &&
            !winName
        "
      >
        {{ gameActiveName }}
      </p>
      <div class="game__map">
        <div
          class="game__item"
          v-for="itemkey in gameSlots"
          v-bind:key="itemkey.key"
          @click="activeblock(itemkey, $event)"
        >
          {{ itemkey.slot }}
        </div>
      </div>
      <p
        v-if="
          gameActiveName != 'Напишите имена' &&
            !winName &&
            gameActiveName != 'Одинаковые имена'
        "
      >
        Ход: {{ gameActiveName }}
      </p>
      <p v-if="winName && winName != 'Ничья'">Победа {{ winName }}</p>
      <p v-if="winName == 'Ничья'">{{ winName }}</p>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  data() {
    return {
      gamesWinSlots: [
        [1, 2, 3],
        [4, 5, 6],
        [7, 8, 9],
        [1, 4, 7],
        [2, 5, 8],
        [3, 6, 9],
        [1, 5, 9],
        [7, 5, 3]
      ],
      gameSlots: [
        { key: 1, slot: "" },
        { key: 2, slot: "" },
        { key: 3, slot: "" },
        { key: 4, slot: "" },
        { key: 5, slot: "" },
        { key: 6, slot: "" },
        { key: 7, slot: "" },
        { key: 8, slot: "" },
        { key: 9, slot: "" }
      ],
      gamerPoint: 0,
      gameActiveName: "Напишите имена",
      gamerNameFirst: null,
      gamerNameSecond: null,
      winName: null
    };
  },
  methods: {
    activeblock(item, event) {
      if (
        this.gameActiveName == "Одинаковые имена" ||
        this.gameActiveName == "Напишите имена" ||
        this.winName
      ) {
        const styleblack = () =>
          (event.target.style.cssText = "#000 !important");
        event.target.addEventListener("transitionend", styleblack, false);
        setTimeout(() => styleblack, 550);
        event.target.style.backgroundColor = "#f35252";
        return;
      }

      if (this.gamerPoint % 2 == 0 && this.gameSlots[item.key - 1].slot == "") {
        event.target.className = "game__item active";
        this.gameSlots[item.key - 1].slot = "X";
        this.gamerPoint += 1;
        this.gameActiveName = this.gamerNameSecond;
        if (this.gamerPoint >= 5) {
          this.checkWin();
        }
      } else if (
        this.gamerPoint % 2 == 1 &&
        this.gameSlots[item.key - 1].slot == ""
      ) {
        event.target.className = "game__item active";
        this.gameSlots[item.key - 1].slot = "O";
        this.gamerPoint += 1;
        this.gameActiveName = this.gamerNameFirst;
        if (this.gamerPoint >= 5) {
          this.checkWin();
        }
      } else {
        const styleblack = () =>
          (event.target.style.cssText = "#000 !important");
        event.target.addEventListener("transitionend", styleblack, false);
        setTimeout(() => styleblack, 550);
        event.target.style.backgroundColor = "#f35252";
      }
    },
    checkWin() {
      this.gamesWinSlots.forEach(itemWinSlot => {
        if (
          this.gameSlots.filter(
            item => itemWinSlot.includes(item.key) && item.slot == "X"
          ).length >= 3
        ) {
          this.winName = this.gamerNameFirst;
          return;
        }
        if (
          this.gameSlots.filter(
            item => itemWinSlot.includes(item.key) && item.slot == "O"
          ).length >= 3
        ) {
          this.winName = this.gamerNameSecond;
          return;
        }
        if (this.gamerPoint == 9) {
          this.winName = "Ничья";
          return;
        }
      });
    },
    inputChangeHandlerFirst(event) {
      this.gamerNameFirst = event.target.value;

      if (this.gamerNameSecond?.length > 0 && this.gamerNameSecond != null) {
        this.gameActiveName = this.gamerNameFirst;
      }
      if (
        this.gamerNameFirst == this.gamerNameSecond &&
        this.gamerNameFirst != "" &&
        this.gamerNameSecond != ""
      ) {
        this.gameActiveName = "Одинаковые имена";
      } else if (this.gamerNameFirst == "" || this.gamerNameSecond == "") {
        this.gameActiveName = "Напишите имена";
      }
    },
    inputChangeHandlerSecond(event) {
      this.gamerNameSecond = event.target.value;
      if (this.gamerNameFirst?.length > 0 && this.gamerNameFirst != null) {
        this.gameActiveName = this.gamerNameFirst;
      }
      if (this.gamerNameFirst == this.gamerNameSecond) {
        this.gameActiveName = "Одинаковые имена";
      } else if (this.gamerNameFirst == "" || this.gamerNameSecond == "") {
        this.gameActiveName = "Напишите имена";
      }
    }
  }
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Montserrat:wght@400;500&display=swap");

.container__inputs {
  display: flex;
  align-items: center;
  flex-direction: column;
  background-color: #000;
  width: 100%;
}
label {
  margin: 10px 0 10px 0;
  color: #fff;
  font-family: "Montserrat";
}
input {
  max-width: 300px;
  padding: 5px 0;
  width: 100%;
  color: #fff;
  font-size: 30px;
  border: solid 2px #fff;
  background-color: #000;
  font-family: "Montserrat";
  text-align: center;
}
input:focus {
  outline: none;
  border: solid 2px #fff;
}

* {
  box-sizing: border-box;
  padding: 0px;
  margin: 0px;
}
#app {
  display: flex;
  align-items: center;
  justify-content: center;
  height: 100vh;
  flex-direction: column;
}
.game__container {
  max-width: 380px;
  display: flex;
  flex-direction: column;
  align-items: center;
  border: 40px #000 solid;
  border-top: 20px solid #000;
}

.game__map {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  justify-content: space-between;
  width: 300px;
  height: 300px;
}
.game__item {
  background-color: black;
  width: 100px;
  height: 100px;
  border: 1px solid white;
  color: white;
  font-family: Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
    sans-serif;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 50px;
  transition: 0.55s ease all;
  cursor: pointer;
  user-select: none;
}

.game__item.active {
  animation: ease-in-out bounce 0.8s;
  width: 100px;
  height: 100px;
  border: 1px solid white;
  color: white;
  font-family: Roboto, Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue",
    sans-serif;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 50px;
  animation-fill-mode: forwards;
}

p {
  width: 100%;
  text-align: center;
  background: #000;
  font-family: "Montserrat";
  font-size: 35px;
  color: white;
  padding: 30px 0 0 0;
}

.lower {
  font-size: 28px;
  padding: 15px 0 30px 0;
}

@keyframes bounce {
  from {
    transform: scale(1);
  }
  50% {
    transform: scale(0.85);
  }
  to {
    transform: scale(1);
  }
}
</style>
