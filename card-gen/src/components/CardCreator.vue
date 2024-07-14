<template>
  <div class="main">
    <div class="cards">
      <div v-for="card in cards" v-bind:key="card">
        <div class="suits">
          <div v-for="suit in suits" v-bind:key="suit">
            <div class="card-holder">
              <Card
              :suitimage="suitimages[suit]"
              :width="63.5"
              :height="88.9"
              :value="card"
              :suitoffsetx="Number.parseFloat(parameters.suitoffsetx.value)"
              :suitoffsety="Number.parseFloat(parameters.suitoffsety.value)"
              :letteroffsetx="0"
              :letteroffsety="0"
              :letterheight="parameters.fontsize.value"
              :suitwidth="parameters.suitwidth.value"
              :numberboxheight="parameters.numberboxheight.value"
              :numberboxwidth="parameters.numberboxwidth.value"
              :numberscale="card == 'A' ? parameters.acescale.value : parameters.numberscale.value"
              :backimage="getBackgroundImage(card, suit)"
              :color="suitToColor(suit)"></Card>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div class="settings-container">
      <div class="parameters">
        <div v-for="obj in parameters" v-bind:key="obj.name">
          <label>
            {{ obj.name }}: 
          </label>
          <input type="number" v-model="obj.value" class="number-parameter">
        </div>
      </div>
      Suit
      <div v-for="card in cards" v-bind:key="card">
        <div v-if="card == 'K' || card == 'Q' || card == 'J'">
          <div v-for="suit in suits" v-bind:key="suit">
            {{ suit }} {{ card }}
            <input :id="'test'+0" type="file" @change="onFileChange($event, card, suit)">
          </div>
        </div>
      </div>
      <div v-for="suit in suits" v-bind:key="suit + 'suit'">
        {{ suit }} image
        <input :id="'test'+0" type="file" @change="onFileChangeSuit($event, suit)">
      </div>
      Background
      <input :id="'test'+0" type="file" @change="cardbackgroundChange($event)">
    </div>
  </div>
</template>

<style>

.number-parameter::-webkit-outer-spin-button,
.number-parameter::-webkit-inner-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
.parameters {
  display: flex;
  flex-direction: column;
  justify-content: left;
  align-items: start;
}
.main {
  display: flex;
}
.settings-container {
  margin-left: 10mm;

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
      parameters: {
        numberscale: {value: 2.26, name: 'Number scale'},
        suitoffsetx: {value: 5.3, name: "Corner suit offset x"},
        suitoffsety: {value: 18, name: "Corner suit offset y"},
        numberboxwidth: {value: 30, name: "Number box width"},
        numberboxheight: {value: 55, name: "Number box height"},
        acescale:    {value: 2.5, name: "Ace scale"},
        suitwidth:    {value: 7.5, name: "Smallest suit image size"},
        fontsize:    {value: 8.8, name: "Font size"},
      },
      numberscale: 1,
      suitoffsetx: '1', // String just because range creates a string
      suitoffsety: '5', // String just because range creates a string
      suitimages: {
        "Spades": undefined,
        "Clubs": undefined,
        "Hearts": undefined,
        "Diamonds": undefined,
      },
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
      this.$forceUpdate();
    },
    onFileChangeSuit(e, suit) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.suitimages[suit] = URL.createObjectURL(files[0]);
      this.$forceUpdate();
    },
    cardbackgroundChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.cardbackground = URL.createObjectURL(files[0]);
      this.$forceUpdate();
    },
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
