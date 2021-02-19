<template>
  <div>
    <div v-for="player in Object.keys(playerPoints)" :key="player">
      {{ player }}: {{ playerPoints[player] }} Punkte
    </div>
    <div class="try-count">
      Wer ist dran? {{ player }}: {{ tryCount }}. Versuch
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
              :src="getImage(1)"
              alt="Avatar"
              style="width:150px;height:150px;"
            />
          </div>
          <div class="flip-card-back relative">
            <div class="absolute flip-message-wrapper">
              <div class="flip-message">{{ message }}</div>
            </div>
            <img
              :src="getImage(counter + 1)"
              alt="Avatar"
              style="width:150px;height:150px;"
            />
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'App',
  components: {},
  mounted() {
    this.player = this.players[0];
    this.messages = this.messages
      .concat(this.messages)
      .sort(() => 0.5 - Math.random());
    //this.messages = this.messages.push('test')
    this.players.forEach((p) => (this.playerPoints[p] = 0));
  },
  data() {
    return {
      players: ['Player 1', 'Player 2', 'Player 3'],
      player: '',
      playerPoints: {},
      messages: [
        'Hello Pedro and Celina, flize navidad',
        'Les deseamos una feliz navidad',
        'Prospero ano nuevo',
      ],
      cardAlreadyClicked: false,
      firstTimeClicked: false,
      tryCount: 1,
      previousMessage: null,
      previousId: null,
    };
  },
  methods: {
    turnCard(message, counter) {
      console.log(this.cardAlreadyClicked);
      if (this.cardAlreadyClicked === true) {
        return;
      }
      this.cardAlreadyClicked = true;
      const flipCard = document.getElementById('flip-card-inner-' + counter);
      flipCard.style.transform = 'rotateY(180deg)';
      setTimeout(() => {
        flipCard.style.transform = 'rotateY(0deg)';
        if (this.firstTimeClicked === true) {
          console.log('this was the second click');
          this.firstTimeClicked = false;
        }
        if (this.firstTimeClicked === false) {
          this.firstTimeClicked = true;
        }
        this.cardAlreadyClicked = false;

        // check if win
        if (this.previousMessage === message) {
          console.log('point')
        }

        // save previous message an id
        if (this.tryCount === 1) {
          this.previousMessage = message
          this.previousId = counter
        } else {
          this.previousMessage = null
          this.previousId = null
        }

        // change player
        if (this.tryCount === 2) {
          if (this.player === this.players[this.players.length - 1]) {
            this.player = this.players[0];
          } else {
            this.player = this.players[this.players.indexOf(this.player) + 1];
          }
        }

        // update tryCount
        this.tryCount = this.tryCount === 1 ? 2 : 1;
      }, 1000);
    },
    getImage(counter) {
      return require(`./assets/${counter}.png`);
    },
  },
};
</script>
<style>
.d-flex {
  display: flex;
}

.try-count {
  margin-top: 10px;
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
</style>
