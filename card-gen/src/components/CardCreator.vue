<template>
  <div class="main">
    Hello
    <div class="cards">
      <div v-for="card in cards" v-bind:key="card">
        <div class="suits">
          <div v-for="suit in suits" v-bind:key="suit">
            <div class="card-holder">
              <Card :suitimage="image"
              :width="63.5"
              :height="88.9"
              :value="card"
              :suitoffsetx="Number.parseFloat(suitoffsetx)"
              :suitoffsety="Number.parseFloat(suitoffsety)"
              :letteroffsetx="0"
              :letteroffsety="0"
              :letterheight="5"
              :backimage="getBackgroundImage(card, suit)"
              :color="suitToColor(suit)"></Card>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="settings-container">
      <div class="slidecontainer">
        <input type="range" min="1" max="20" step="0.01" v-model="suitoffsetx">
        {{ suitoffsetx }}
      </div>
      <div>
        <div class="slidecontainer">
          <input type="range" min="5" max="20" step="0.01" v-model="suitoffsety">
          {{ suitoffsety }}
        </div>
      </div>
      Suit
      <div v-for="card in cards" v-bind:key="card">
        <div v-if="card == 'k' || card == 'Q' || card == 'J'">
          <div v-for="suit in suits" v-bind:key="suit">
            {{ suit }} {{ card }}
            <input :id="'test'+0" type="file" @change="onFileChange($event, card, suit)">
          </div>
        </div>
      </div>
      Background
      <input :id="'test'+0" type="file" @change="cardbackgroundChange($event)">
    </div>
  </div>
</template>

<style>
.main {
  display: flex;
}
.settings-container {

}
.cards {
  display: flex;
  flex-direction: column;
}
.suits {
  display: flex;
  flex-direction: row;
}
.card-holder {
  border: 1px black solid;
  width: max-content;
}
</style>

<script>
import Card from './Card.vue'

export default {
  name: 'CardCreator',
  components: {
    Card
  },
  data() {
    return {
      image: undefined,
      cardbackground: undefined,
      cards: [
        'A',
        '2',
        '3',
        '4',
        '5',
        '6',
        '7',
        '8',
        '9',
        '10',
        'J',
        'Q',
        'K',
      ],
      suits: [
        'Spades',
        'Clubs',
        'Hearts',
        'Diamonds',
      ],
      i:'',
      imageplace: 0,
      suitoffsetx: '1', // String just because range creates a string
      suitoffsety: '5', // String just because range creates a string
      images: {
        'A': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '2': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '3': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '4': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '5': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '6': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '7': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '8': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '9': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        '10': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        'J': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        'Q': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
        'K': {
          "Spades": undefined,
          "Clubs": undefined,
          "Hearts": undefined,
          "Diamonds": undefined
        },
      },
    }
  },
  methods: {
    suitToColor(s) {
      switch (s) {
        case "Spades": return "black";
        case "Hearts": return "red";
        case "Diamonds": return "red";
        case "Clubs": return "black";
      }
    },
    getBackgroundImage(card, suit) {
      return this.images[card][suit];
    },
    onFileChange(e, card, suit) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.images[card][suit] = URL.createObjectURL(files[0]);
      console.log(this.image)
      this.$forceUpdate();
    },
    cardbackgroundChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.cardbackground = URL.createObjectURL(files[0]);
      console.log(this.cardbackground)
      this.$forceUpdate();
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
