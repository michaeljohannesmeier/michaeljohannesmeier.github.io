<template>
  <div class="margin-app-bar relative">
    <div>
      <!-- Enter player names -->
      <div v-if="mode === 'enterPlayer'" class="ma-12">
        <div class="d-flex align-center justify-center">
          <div class="w-200 mr-12">
            <v-text-field
              label="Spielername"
              v-model="playerName"
            ></v-text-field>
          </div>

          <v-btn @click="addPlayer" :disabled="players.includes(playerName)">
            Spieler hinzufügen
          </v-btn>
        </div>
        <div class="d-flex justify-center w-full mt-12">
          <v-btn
            @click="startGame"
            color="green"
            :disabled="players.length === 0"
          >
            Spiel starten
          </v-btn>
        </div>
        <div class="mt-12 d-flex justify-center" v-if="players.length > 0">
          Spieler:
        </div>
        <div class="d-flex flex-column align-center justify-center mt-6">
          <div
            v-for="(player, i) in players"
            :key="player"
            class="d-flex mb-6"
          >
            <v-avatar size="26" :color="colors[i]" class="mr-2">
              <v-icon dark>
                mdi-account-circle
              </v-icon>
            </v-avatar>
            <div class="mb-1">
              {{ player }}
            </div>
          </div>
        </div>
      </div>

      <!-- Game -->
      <div v-else>
        <div class="d-flex align-center justify-center">
          <div
            v-for="(player, i) in Object.keys(playerPoints)"
            :key="player"
            class="mr-6 ml-6 d-flex"
          >
            <v-avatar size="26" :color="colors[i]" class="mr-2">
              <v-icon dark>
                mdi-account-circle
              </v-icon>
            </v-avatar>
            <div class="mb-1">
              {{ player }}: {{ playerPoints[player] }} Punkte
            </div>
          </div>
        </div>
        <div class="try-count w-100 d-flex justify-center mt-6">
          <div class="w-600">
            <v-carousel
              v-model="playerId"
              hide-delimiters
              hide-delimiter-background
              :show-arrows="false"
              height="50"
            >
              <v-carousel-item
                v-for="(player, i) in players"
                :key="`${player}`"
              >
                <v-sheet :color="colors[i]" height="100%" tile>
                  <v-row class="fill-height" align="center" justify="center">
                    <div class="player-info mt-3">
                      <span class="mr-6">{{ player }}</span> -
                      <span class="ml-6">{{ tryCount }}. Versuch</span>
                    </div>
                  </v-row>
                </v-sheet>
              </v-carousel-item>
            </v-carousel>
          </div>
        </div>

        <div class="cards-wrapper">
          <div
            class="flip-card"
            @click="turnCard(message, counter)"
            v-for="(message, counter) in messages"
            :key="counter"
          >
            <div class="flip-card-inner" :id="`flip-card-inner-${counter}`">
              <div class="flip-card-front">
                <img
                  :src="getImage('deck')"
                  alt="Avatar"
                  style="width:150px;height:150px;"
                />
              </div>
              <div class="flip-card-back relative">
                <div class="absolute flip-message-wrapper">
                  <!-- <div class="flip-message">{{ message }}</div> -->
                </div>
                <img
                  :src="getImage(message)"
                  alt="Avatar"
                  style="width:150px;height:150px;"
                />
              </div>
            </div>
          </div>
        </div>
      </div>

      <!-- Game finished -->
      <v-alert
        border="left"
        color="green"
        dark
        v-if="gameOver"
        class="game-over d-flex justify-center align-center"
      >
        <div class="d-flex justify-center">
          Spieler {{ playersWon.join(' und ') }}
          {{ playersWon.length === 1 ? 'hat' : 'haben' }} gewonnen!!
        </div>
        <div class="d-flex justify-center mt-12">
          <v-btn @click="startGame" color="blue" class="mr-12">
            Spiel neu starten
          </v-btn>
          <v-btn @click="startNew" color="orange">
            Namen neu eingeben
          </v-btn>
        </div>
      </v-alert>
    </div>
    <v-snackbar v-model="snackbar" color="success">
      Juhuuu!!!! Treffer !!!
    </v-snackbar>
  </div>
</template>

<script>
export default {
  name: 'Memory',

  components: {},

  mounted() {},
  data() {
    return {
      players: [],
      player: '',
      playerName: '',
      playerId: 0,
      playersWon: [],
      playerPoints: {},
      snackbar: false,
      allMessages: [
        'Hello Pedro and Celina, flize navidad',
        'Les deseamos una feliz navidad',
        'Prospero ano nuevo',
      ],
      messages: null,
      cardAlreadyClicked: false,
      firstTimeClicked: false,
      tryCount: 1,
      previousMessage: null,
      previousId: null,
      openCards: [],
      gameOver: false,
      mode: 'enterPlayer',
      colors: ['orange', 'blue', 'indigo', 'black', 'grey', 'green', 'yellow'],
    };
  },
  methods: {
    // Add player
    addPlayer() {
      this.players.push(this.playerName);
      this.playerName = '';
    },
    resetValues() {
      this.player = '';
      this.playerName = '';
      this.playerId = 0;
      this.playersWon = [];
      this.playerPoints = {};
      this.cardAlreadyClicked = false;
      this.firstTimeClicked = false;
      this.tryCount = 1;
      this.previousMessage = null;
      this.previousId = null;
      this.openCards = [];
      this.gameOver = false;
      this.mode = 'enterPlayer';
      this.player = this.players[0];
      this.messages = null;
      this.messages = this.allMessages
        .concat(this.allMessages)
        .sort(() => 0.5 - Math.random());
      //this.messages = this.messages.push('test')
      this.players.forEach((p) => (this.playerPoints[p] = 0));
    },

    startGame() {
      this.resetValues();
      this.mode = 'game';
      this.$nextTick(() => {
        this.messages.forEach((m, i) => {
          const flipCard = document.getElementById('flip-card-inner-' + i);
          flipCard.style.transform = 'rotateY(0deg)';
        });
      });
    },
    startNew() {
      this.players = [];
      this.resetValues();
      this.mode = 'enterPlayer';
    },

    // Game
    turnCard(message, counter) {
      console.log(this.cardAlreadyClicked);
      if (this.cardAlreadyClicked === true) {
        return;
      }
      if (this.openCards.includes(counter)) {
        return;
      }
      this.cardAlreadyClicked = true;
      const flipCard = document.getElementById('flip-card-inner-' + counter);
      flipCard.style.transform = 'rotateY(180deg)';
      setTimeout(() => {
        if (this.firstTimeClicked === true) {
          console.log('this was the second click');
          this.firstTimeClicked = false;
        }
        if (this.firstTimeClicked === false) {
          this.firstTimeClicked = true;
        }
        this.cardAlreadyClicked = false;

        // check if win
        let wasHit = false;
        if (this.previousMessage === message) {
          wasHit = true;
          console.log('point');
          this.snackbar = true;
          this.playerPoints[this.player] += 1;
          const idsToOpen = [];
          this.messages.forEach((m, index) =>
            m === message ? idsToOpen.push(index) : null
          );
          idsToOpen.forEach((i) => {
            const flipCardWin = document.getElementById('flip-card-inner-' + i);
            flipCardWin.style.transform = 'rotateY(180deg)';
            this.openCards.push(i);
            if (this.openCards.length === this.messages.length) {
              this.gameOver = true;
              // get player won
              let maxPoints = 0;
              Object.keys(this.playerPoints).forEach((player) => {
                if (this.playerPoints[player] > maxPoints) {
                  maxPoints = this.playerPoints[player];
                }
              });
              Object.keys(this.playerPoints).forEach((player) => {
                if (this.playerPoints[player] === maxPoints) {
                  this.playersWon.push(player);
                }
              });
            }
          });
        }

        // change player
        if (!wasHit) {
          if (this.tryCount === 2) {
            flipCard.style.transform = 'rotateY(0deg)';
            const prevFlipCard = document.getElementById(
              'flip-card-inner-' + this.previousId
            );
            prevFlipCard.style.transform = 'rotateY(0deg)';
          }
          if (this.tryCount === 2) {
            if (this.player === this.players[this.players.length - 1]) {
              this.player = this.players[0];
              this.playerId = 0;
            } else {
              this.player = this.players[this.players.indexOf(this.player) + 1];
              this.playerId += 1;
            }
          }
        }

        // save previous message an id
        if (this.tryCount === 1) {
          this.previousMessage = message;
          this.previousId = counter;
        } else {
          this.previousMessage = null;
          this.previousId = null;
        }

        // update tryCount
        this.tryCount = this.tryCount === 1 ? 2 : 1;
      }, 1000);
    },
    getImage(message) {
      console.log(message);
      if (message === 'deck') {
        return require(`../assets/deck.jpg`);
      } else {
        const i = this.messages.indexOf(message) + 1;
        return require(`../assets/${i}.png`);
      }
    },
  },
};
</script>
<style>
.margin-app-bar {
  margin-top: 140px;
}

.w-100 {
  width: 100%;
}
.w-200 {
  width: 200px;
  max-width: 200px;
}
.w-600 {
  width: 600px;
}
.w-full {
  width: 100%;
}

.h-200 {
  height: 200px;
  max-height: 200px;
}

.cards-wrapper {
  display: flex;
  flex-wrap: wrap;
}

.flip-card {
  background-color: transparent;
  width: 150px;
  height: 150px;
  perspective: 1000px;
  cursor: pointer;
  margin: 50px;
}

.flip-card-inner {
  position: relative;
  width: 100%;
  height: 100%;
  text-align: center;
  transition: transform 0.6s;
  transform-style: preserve-3d;
  box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2);
}

.flip-card-front,
.flip-card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  -webkit-backface-visibility: visible;
  backface-visibility: visible;
}

.flip-card-front {
  background-color: #bbb;
  color: black;
}

.flip-card-back {
  background-color: #2980b9;
  color: white;
  transform: rotateY(180deg);
}

.relative {
  position: relative;
}

.absolute {
  position: absolute;
}

.flip-message-wrapper {
  background-color: rgba(0, 0, 0, 0.5);
  width: 100%;
  position: flex;
  justify-content: center;
}

.flip-message {
  padding: 4px;
}

.card-open {
  transform: rotateY(180deg);
}

.game-over {
  position: fixed !important;
  top: 30%;
  height: 300px;
  width: 100%;
  margin-left: auto;
  margin-right: auto;
  opacity: 0.9;
}
</style>
