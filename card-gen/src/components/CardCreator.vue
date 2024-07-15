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
              :color="suitToColor(suit)"
              :output="shouldDownload"></Card>
            </div>
          </div>
        </div>
      </div>
      <div class="card-holder">
        <Card
          :letteroffsetx="0"
          :letteroffsety="0"
          :suitwidth="parameters.suitwidth.value"
          :suitoffsetx="Number.parseFloat(parameters.suitoffsetx.value)"
          :suitoffsety="Number.parseFloat(parameters.suitoffsety.value)"
          :letterheight="parameters.fontsize.value"
          :value="'Joker'"
          :width="63.5"
          :height="88.9"
          :backimage="joker_1"
          :color="'black'"
          :output="shouldDownload">
        </Card>
      </div>
      <div class="card-holder">
        <Card
          :letteroffsetx="0"
          :letteroffsety="0"
          :suitwidth="parameters.suitwidth.value"
          :suitoffsetx="Number.parseFloat(parameters.suitoffsetx.value)"
          :suitoffsety="Number.parseFloat(parameters.suitoffsety.value)"
          :letterheight="parameters.fontsize.value"
          :value="'Joker'"
          :width="63.5"
          :height="88.9"
          :color="'red'"
          :backimage="joker_2"
          :output="shouldDownload">
      </Card>
      </div>
    </div>
    <div class="settings-container">
      <input class="download-button" type="button" value="DOWNLOAD ALL" @click="downloadAll()">
      <div class="parameters">
        <div v-for="obj in parameters" v-bind:key="obj.name">
          <label>
            {{ obj.name }}: 
          </label>
          <input type="number" v-model="obj.value" class="number-parameter">
        </div>
      </div>
      <br>
      <div class="file-upload">
        <h3>Upload folder with the following files:</h3>
        <ul class="instruction-list">
          <li>spade.png</li>
          <li>club.png</li>
          <li>heart.png</li>
          <li>diamond.png</li>

          <li>king_spade.png</li>
          <li>king_club.png</li>
          <li>king_heart.png</li>
          <li>king_diamond.png</li>
          <li>queen_spade.png</li>
          <li>queen_club.png</li>
          <li>queen_heart.png</li>
          <li>queen_diamond.png</li>
          <li>jack_spade.png</li>
          <li>jack_club.png</li>
          <li>jack_heart.png</li>
          <li>jack_diamond.png</li>
          <li>joker_1.png</li>
          <li>joker_2.png</li>
        </ul>
        <input type="file" webkitdirectory mozdirectory @change="onFolderChange($event)">
      </div>
      <!-- <div v-for="card in cards" v-bind:key="card">
        <div v-if="card == 'K' || card == 'Q' || card == 'J'">
          <div v-for="suit in suits" v-bind:key="suit">
            {{ suit }} {{ card }}
          </div>
        </div>
      </div> -->
      <div v-for="suit in suits" v-bind:key="suit + 'suit'">
        {{ suit }} image
        <input :id="'test'+0" type="file" @change="onFileChangeSuit($event, suit)">
      </div>
    </div>
  </div>
</template>

<style>
.download-button {
  margin: 5mm;
  font-size: 25mm;
}
.file-upload {
  justify-content: left;
  display: flex;
  flex-direction: column;
}
.instruction-list {
  text-align: left;
}
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

function isFunction(functionToCheck) {
 return functionToCheck && {}.toString.call(functionToCheck) === '[object Function]';
}

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
      shouldDownload: false,
      parameters: {
        numberscale: {value: 2.26, name: 'Number scale'},
        suitoffsetx: {value: 9, name: "Corner suit offset x"},
        suitoffsety: {value: 18, name: "Corner suit offset y"},
        numberboxwidth: {value: 26, name: "Number box width"},
        numberboxheight: {value: 52, name: "Number box height"},
        acescale:    {value: 2.5, name: "Ace scale"},
        suitwidth:    {value: 7.5, name: "Smallest suit image size"},
        fontsize:    {value: 8.8, name: "Font size"},
      },
      joker_1: undefined,
      joker_2: undefined,
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
    onFolderChange(e) {
      console.log("FILES", e.target.files)
      for (var idx in e.target.files) {
        var file = e.target.files[idx]
        if (!file.name || isFunction(file)) {
          continue;
        }
        console.log("FILE", file)
        var vals = file.name.split("_");
        console.log(vals);
        if (vals.length < 2) {
          var suit = file.name.split(".")[0];
          suit = suit.charAt(0).toUpperCase() + suit.slice(1).toLowerCase();
          if (suit.charAt(suit.length-1) != "s") {
            suit = suit + "s";
          }
          console.log("Result", suit, card)
          this.suitimages[suit] = URL.createObjectURL(file);
        } else if (!vals[0].toLowerCase().includes("joker")) {
          var card = vals[0].charAt(0).toUpperCase();
          var suit = vals[1].split(".")[0];
          suit = suit.charAt(0).toUpperCase() + suit.slice(1).toLowerCase();
          if (suit.charAt(suit.length-1) != "s") {
            suit = suit + "s";
          }
          console.log("Result cards", suit, card)
          this.images[card][suit] = URL.createObjectURL(file);
        } else {
          var name = file.name.split(".")[0];
          if (name.includes("joker")) {
            if (name.includes("1")) {
              this.joker_1 = URL.createObjectURL(file);
            } else if (name.includes("2")) {
              this.joker_2 = URL.createObjectURL(file);
            }
          }
        }
      }
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
      console.log(files[0])
      this.$forceUpdate();
    },
    cardbackgroundChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.cardbackground = URL.createObjectURL(files[0]);
      this.$forceUpdate();
    },
    downloadAll() {
      this.shouldDownload = true;
      console.log("DOWNLOADING")
      var self = this;
      setTimeout(function () {
        self.shouldDownload = false;
      }, 500)
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
