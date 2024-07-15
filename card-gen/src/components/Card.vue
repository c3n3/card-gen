<script setup lang="ts"></script>
<template>
    <div @click="download()" :id="'card-' + unique" :style="physicalStyle()" class="card">
        <div :style="topLetterStyle()">{{ value }}</div>
        <div :style="bottomLetterStyle()">{{ value }}</div>
        <img v-if="suitimage" :src="suitimage" :width="mmToPx(this.suitwidth)" :height="mmToPx(this.suitwidth)" class="suit" :style="topSuitStyle()"/>
        <img v-if="suitimage" :src="suitimage" :width="mmToPx(this.suitwidth)" :height="mmToPx(this.suitwidth)" class="suit" :style="bottomSuitStyle()"/>
        <img v-if="backimage" :src="backimage" :width="mmToPx(this.width)" :height="mmToPx(this.height)" :style="backimageStyle()"/>
        <Numbers :x="numberBoxX()" :y="numberBoxY()" :suitimage="suitimage" :number="value" :width="numberboxwidth" :height="numberboxheight" :imagewidth="this.suitwidth * this.numberscale"></Numbers>
    </div>
</template>
<style scoped>
.card {
    font-family: "Comic Sans MS", "Comic Sans", cursive;
    position: relative;
}
.card :hover {
    cursor: pointer;
}

.card-title {
    white-space: pre-line;
    font-weight: bold;
    text-align: center;
    flex-grow: 1;
}
</style>


<script>

let uid = 0;
import Numbers from './Numbers.vue'

import { defineComponent } from 'vue'

import * as htmlToImage from 'html-to-image';
import * as download from 'downloadjs';

export default {
    name: 'Card',
    components: {
        Numbers
    },
    props: [
        'value',
        'color',
        'height',
        'width',
        'suitimage',
        'backimage',
        'suitoffsetx',
        'suitoffsety',
        "letteroffsetx",
        "letteroffsety",
        "letterheight",

        // For cards 2 - 10
        "numberboxwidth",
        "numberboxheight",
        "numberxspread",
        "numberyspread",
        "numberscale",

        "acescale",

        "suitwidth",

        "output"
    ],
    data() {
        return {
            scale: 1.0294,
            unique: 1,
            pos: {x: 0, y: 0},
        }
    },
    methods: {
        physicalStyle() {
            return `width: ${this.width}mm;
                    height: ${this.height}mm;
                    color: ${this.color};
        `
        },
        download() {
            var self = this;
            htmlToImage.toPng(document.getElementById('card-' + this.unique),
                { 'canvasWidth': this.mmToPx(this.width) * 5, 'canvasHeight': this.mmToPx(this.height) * 5 })
                .then(function (dataUrl) {
                    (download)(dataUrl, 'card-' + self.unique + '.png');
                })
                .catch(function (error) {
                    console.error('oops, something went wrong!', error);
                });
        },
        pxToMm(px) {
            var div = document.createElement('div');
            div.style.display = 'block';
            div.style.height = '100mm';
            document.body.appendChild(div);
            var convert = 100 / div.offsetHeight;
            convert = px * convert;
            div.parentNode.removeChild(div);
            return convert;
        },
        mmToPx(mm) {
            var div = document.createElement('div');
            div.style.display = 'block';
            div.style.height = '100mm';
            document.body.appendChild(div);
            var convert = div.offsetHeight / 100;
            convert = mm * convert;
            div.parentNode.removeChild(div);
            return convert;
        },
        getAbsPos(element) {
            var top = 0, left = 0;
            if (!element) {
                return {x: 0, y: 0};
            }
            do {
                top += element.offsetTop || 0;
                left += element.offsetLeft || 0;
                element = element.offsetParent;
            } while (element);

            return {
                y: this.pxToMm(top),
                x: this.pxToMm(left)
            };
        },
        backimageStyle() {
            if (this.pos && this.backimage) {
                var ret = `
                    position: absolute;
                    top: 0mm;
                    left: 0mm;
                `
                return ret;
            } else {
                return "display: none;"
            }
        },
        topSuitStyle() {
            if (this.pos && this.suitoffsetx && this.suitoffsety && this.suitimage) {
                var ret = `
                    position: absolute;
                    top: ${this.suitoffsety - this.suitwidth/2}mm;
                    left: ${this.suitoffsetx - this.suitwidth/2}mm;
                `
                return ret;
            } else {
                return "display: none;"
            }
        },
        topLetterStyle() {
            var chk = function(arg) {
                return arg != undefined;
            };
            if (this.pos && chk(this.letteroffsetx) && chk(this.letteroffsety) && chk(this.letterheight)) {
                var ret = `
                    position: absolute;
                    top: ${this.suitoffsety - this.letterheight - this.suitwidth/2}mm;
                    left: ${this.suitoffsetx - this.suitwidth/2}mm;
                    font-size: ${this.letterheight}mm;
                    height: ${this.letterheight}mm;
                    width: ${this.suitwidth}mm;
                `
                return ret;
            } else {
                return ""
            }
        },
        bottomLetterStyle() {
            var chk = function(arg) {
                return arg != undefined;
            };
            if (this.pos && chk(this.letteroffsetx) && chk(this.letteroffsety) && chk(this.letterheight)) {
                var ret = `
                    position: absolute;
                    top: ${this.height - this.suitoffsety + this.suitwidth/2}mm;
                    left: ${this.width - this.suitwidth/2 - this.suitoffsetx}mm;
                    font-size: ${this.letterheight}mm;
                    height: ${this.letterheight}mm;
                    width: ${this.suitwidth}mm;
                    rotate: 180deg
                `
                return ret;
            } else {
                return ""
            }
        },
        bottomSuitStyle() {
            if (this.pos != undefined && this.suitoffsetx != undefined && this.suitoffsety != undefined && this.suitimage) {
                var ret = `
                    position: absolute;
                    top: ${this.height - this.suitwidth/2 - this.suitoffsety}mm;
                    left: ${this.width - this.suitwidth/2 - this.suitoffsetx}mm;
                    rotate: 180deg;
                `
                return ret;
            } else {
                return "display: none;"
            }
        },
        position() {
            return this.getAbsPos(document.getElementById('card-' + this.unique));
        },
        numberBoxX() {
            var ret = (this.width - this.numberboxwidth)/2;
            return ret;
        },
        numberBoxY() {
            return (this.height - this.numberboxheight)/2;
        },
        getSuitImageLocationTop() {
            return {
                x: this.pos.x + this.suitoffset.x,
                y: this.pos.y + this.suitoffset.y
            }
        },
        isFace() {
            return this.value == 'Q' || this.value == 'K' || this.value == 'J' || this.value == 'Joker';
        },
    },
    created() {
        this.unique = uid;
        uid++
    },
    mounted() {
        this.pos = this.position();
    },
    watch: {
        output: function (old, newVal) {
            if (newVal) {
                this.download()
            }
        }
    },
}
</script>
