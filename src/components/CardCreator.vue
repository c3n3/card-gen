<template>
  <div class="main">
    <div class="cards">
      <div v-for="card in cards" v-bind:key="card">
        <div class="suits">
          <div v-for="suit in suits" v-bind:key="suit">
            <div class="card-holder">
              <Card
              :suitimageOuter="suitimagesOuter[suit] || suitimages[suit]"
              :suitimageInner="suitimagesInner[suit] || suitimages[suit]"
              :suitimage="suitimagesOuter[suit] || suitimages[suit]"
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
              :fontFamily="selectedFont"
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
          :fontFamily="selectedFont"
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
          :fontFamily="selectedFont"
          :output="shouldDownload">
      </Card>
      </div>
    </div>
    <div class="settings-container">
      <input class="download-button" type="button" value="DOWNLOAD ALL" @click="downloadAll()">

      <!-- Font selection -->
      <div class="font-section">
        <h3>Font</h3>
        <label>Font family: </label>
        <select v-model="selectedFont" class="font-select">
          <option v-for="font in availableFonts" :key="font" :value="font" :style="{ fontFamily: font }">{{ font }}</option>
        </select>
        <br>
        <label>Upload TTF font: </label>
        <input type="file" accept=".ttf,.otf,.woff,.woff2" @change="onFontUpload($event)">
      </div>

      <!-- Parameters with sliders -->
      <div class="parameters">
        <div v-for="(obj, key) in parameters" v-bind:key="obj.name" class="parameter-row">
          <label>{{ obj.name }}: </label>
          <template v-if="obj.type === 'number'">
            <input type="range"
              :min="obj.default * 0.5"
              :max="obj.default * 1.5"
              :step="obj.default * 0.01"
              v-model.number="obj.value"
              class="parameter-slider">
            <input type="number" v-model="obj.value" class="number-parameter" :step="obj.default * 0.01">
          </template>
          <template v-else-if="obj.type === 'checkbox'">
            <input type="checkbox" v-model="obj.value">
          </template>
          <template v-else>
            <input type="text" v-model="obj.value">
          </template>
        </div>
      </div>
      <br>
      <div class="file-upload">
        <h3>Upload folder with the following files:</h3>
        <ul class="instruction-list">
          <li>spade.png (used for both inner &amp; outer)</li>
          <li>spade_inner.png (optional, inner only)</li>
          <li>spade_outer.png (optional, outer only)</li>
          <li>club.png / club_inner.png / club_outer.png</li>
          <li>heart.png / heart_inner.png / heart_outer.png</li>
          <li>diamond.png / diamond_inner.png / diamond_outer.png</li>

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
      <div v-for="suit in suits" v-bind:key="suit + 'suit'" class="suit-upload-section">
        <strong>{{ suit }}</strong>
        <div>
          {{ suit }} image (both)
          <input type="file" @change="onFileChangeSuit($event, suit)">
        </div>
        <div>
          {{ suit }} outer image
          <input type="file" @change="onFileChangeSuitOuter($event, suit)">
        </div>
        <div>
          {{ suit }} inner image
          <input type="file" @change="onFileChangeSuitInner($event, suit)">
        </div>
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
.number-parameter {
  width: 80px;
}
.parameters {
  display: flex;
  flex-direction: column;
  justify-content: left;
  align-items: start;
}
.parameter-row {
  display: flex;
  align-items: center;
  gap: 8px;
  margin-bottom: 4px;
}
.parameter-slider {
  width: 120px;
}
.font-section {
  text-align: left;
  margin-bottom: 10px;
}
.font-select {
  margin-left: 4px;
  padding: 2px 4px;
}
.suit-upload-section {
  text-align: left;
  margin-top: 8px;
  padding: 4px 0;
  border-bottom: 1px solid #ccc;
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
      selectedFont: 'Comic Sans MS',
      availableFonts: [
        'Comic Sans MS',
        'Arial',
        'Times New Roman',
        'Georgia',
        'Courier New',
        'Verdana',
        'Impact',
        'Trebuchet MS',
        'Palatino Linotype',
        'Lucida Console',
        'Tahoma',
        'Garamond',
      ],
      uploadedFontCount: 0,
      parameters: {
        numberscale: {value: 2.26, default: 2.26, name: 'Number scale', type: 'number'},
        suitoffsetx: {value: 9, default: 9, name: "Corner suit offset x", type: 'number'},
        suitoffsety: {value: 18, default: 18, name: "Corner suit offset y", type: 'number'},
        numberboxwidth: {value: 26, default: 26, name: "Number box width", type: 'number'},
        numberboxheight: {value: 52, default: 52, name: "Number box height", type: 'number'},
        acescale:    {value: 2.5, default: 2.5, name: "Ace scale", type: 'number'},
        suitwidth:    {value: 7.5, default: 7.5, name: "Smallest suit image size", type: 'number'},
        fontsize:    {value: 8.8, default: 8.8, name: "Font size", type: 'number'},
      },
      joker_1: undefined,
      joker_2: undefined,
      suitimages: {
        "Spades": undefined,
        "Clubs": undefined,
        "Hearts": undefined,
        "Diamonds": undefined,
      },
      suitimagesInner: {
        "Spades": undefined,
        "Clubs": undefined,
        "Hearts": undefined,
        "Diamonds": undefined,
      },
      suitimagesOuter: {
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
          // Single name like spade.png -> goes to both inner and outer
          var suit = file.name.split(".")[0];
          suit = suit.charAt(0).toUpperCase() + suit.slice(1).toLowerCase();
          if (suit.charAt(suit.length-1) != "s") {
            suit = suit + "s";
          }
          console.log("Result suit (both)", suit)
          this.suitimages[suit] = URL.createObjectURL(file);
        } else if (vals.length == 2 && (vals[1].split(".")[0].toLowerCase() === "inner" || vals[1].split(".")[0].toLowerCase() === "outer")) {
          // e.g. spade_inner.png or spade_outer.png
          var suitName = vals[0].charAt(0).toUpperCase() + vals[0].slice(1).toLowerCase();
          if (suitName.charAt(suitName.length-1) != "s") {
            suitName = suitName + "s";
          }
          var variant = vals[1].split(".")[0].toLowerCase();
          if (variant === "inner") {
            this.suitimagesInner[suitName] = URL.createObjectURL(file);
          } else {
            this.suitimagesOuter[suitName] = URL.createObjectURL(file);
          }
          console.log("Result suit " + variant, suitName)
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
    onFileChangeSuitOuter(e, suit) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.suitimagesOuter[suit] = URL.createObjectURL(files[0]);
      this.$forceUpdate();
    },
    onFileChangeSuitInner(e, suit) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.suitimagesInner[suit] = URL.createObjectURL(files[0]);
      this.$forceUpdate();
    },
    onFontUpload(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      var file = files[0];
      var fontName = file.name.replace(/\.[^/.]+$/, "");
      this.uploadedFontCount++;
      var fontFace = new FontFace(fontName, `url(${URL.createObjectURL(file)})`);
      fontFace.load().then((loadedFace) => {
        document.fonts.add(loadedFace);
        if (!this.availableFonts.includes(fontName)) {
          this.availableFonts.push(fontName);
        }
        this.selectedFont = fontName;
      }).catch((err) => {
        console.error("Failed to load font:", err);
      });
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
