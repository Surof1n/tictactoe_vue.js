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
    <div class="menu__container active">
      <ul class="menu__list">
        <li @click="gamepeoples($event)" class="menu__list-item">
          <i class="icofont-users-alt-4"></i>
          <div class="menu__item-text">Двое игроков</div>
        </li>
        <li @click="gamepc($event)" class="menu__list-item">
          <i class="icofont-computer"></i>
          <div class="menu__item-text">C компьютером</div>
        </li>
      </ul>
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
        [7, 5, 3],
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
        { key: 9, slot: "" },
      ],
      gamerPoint: 0,
      gameActiveName: "Напишите имена",
      gamerNameFirst: null,
      gamerNameSecond: null,
      winName: null,
    };
  },
  methods: {
    gamepeoples(event) {
      document.querySelector(".container__inputs").className =
        "container__inputs active";
      document.querySelector(".game__container").className =
        "game__container active";
      document.querySelector(".menu__container.active").className =
        "menu__container";
    },
    gamepc(event) {
      document.querySelector(".game__container").className =
        "game__container active";
      document.querySelector(".menu__container.active").className =
        "menu__container";
      this.gamerNameFirst = "Игрок";
      this.gamerNameSecond = "Компьютер";
      this.gameActiveName = this.gamerNameFirst;
    },
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
        if (this.gameActiveName == "Компьютер" && this.gamerPoint != 9) {
          setTimeout(() => {
            const noactiveLenght = Math.floor(
              this.gameSlots.filter((item) => item.slot == "").length
            );
            const randomIndex = Math.floor(
              Math.random() * (noactiveLenght - 0) + 0
            );
            this.gameSlots.filter((item) => item.slot == "")[randomIndex].slot =
              "O";
            this.gamerPoint += 1;
            this.gameActiveName = this.gamerNameFirst;
            if (this.gamerPoint >= 5) {
              this.checkWin();
            }
          });
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
      this.gamesWinSlots.forEach((itemWinSlot) => {
        if (
          this.gameSlots.filter(
            (item) => itemWinSlot.includes(item.key) && item.slot == "X"
          ).length >= 3
        ) {
          this.winName = this.gamerNameFirst;

          return;
        } else if (
          this.gameSlots.filter(
            (item) => itemWinSlot.includes(item.key) && item.slot == "O"
          ).length >= 3
        ) {
          this.winName = this.gamerNameSecond;
          return;
        } else if (this.gamerPoint == 9 && !this.winName) {
          console.log(
            this.gameSlots.filter(
              (item) => itemWinSlot.includes(item.key) && item.slot == "X"
            ).length
          );
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
    },
  },
};
</script>
<style src="./icofont/icofont.min.css"></style>
<style src="./style.css"></style>
