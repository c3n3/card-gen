<script setup lang="ts"></script>
<template>
    <div @click="download()" :id="'card-' + unique" :style="physicalStyle()" class="card">
        <div :style="topLetterStyle()">{{ value }}</div>
        <div :style="bottomLetterStyle()">{{ value }}</div>
        <img :src="suitimage" :width="mmToPx(this.suitWidth)" :height="mmToPx(this.suitWidth)" class="suit" :style="topSuitStyle()"/>
        <img :src="suitimage" :width="mmToPx(this.suitWidth)" :height="mmToPx(this.suitWidth)" class="suit" :style="bottomSuitStyle()"/>
        <img :src="backimage" :width="mmToPx(this.width)" :height="mmToPx(this.height)" :style="backimageStyle()"/>
        <Numbers :x="numberBoxX()" :y="numberBoxY()" :suitimage="suitimage" :number="value" :width="numberboxwidth" :height="numberboxheight" :imagewidth="this.suitWidth * this.numberscale"></Numbers>
    </div>
</template>
<style scoped>
.card {
    font-family: "Comic Sans MS", "Comic Sans", cursive;
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
    ],
    data() {
        return {
            scale: 1.0294,
            unique: 1,
            pos: {x: 0, y: 0},
            suitWidth: 10,
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
                { 'canvasWidth': this.mmToPx(this.width), 'canvasHeight': this.mmToPx(this.height) })
                .then(function (dataUrl) {
                    (download)(dataUrl, 'card-' + '-' + self.unique + '-' + '.png');
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
            if (this.pos) {
                var ret = `
                    position: absolute;
                    top: ${this.pos.y}mm;
                    left: ${this.pos.x}mm;
                `
                return ret;
            } else {
                return ""
            }
        },
        topSuitStyle() {
            if (this.pos && this.suitoffsetx && this.suitoffsety) {
                var ret = `
                    position: absolute;
                    top: ${this.pos.y + this.suitoffsety}mm;
                    left: ${this.pos.x + this.suitoffsetx}mm;
                `
                return ret;
            } else {
                return ""
            }
        },
        topLetterStyle() {
            var chk = function(arg) {
                return arg != undefined;
            };
            if (this.pos && chk(this.letteroffsetx) && chk(this.letteroffsety) && chk(this.letterheight)) {
                var ret = `
                    position: absolute;
                    top: ${this.pos.y + this.suitoffsety - this.letterheight}mm;
                    left: ${this.pos.x + this.suitoffsetx}mm;
                    font-size: ${this.letterheight}mm;
                    height: ${this.letterheight}mm;
                    width: ${this.suitWidth}mm;
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
                    top: ${this.pos.y + this.height - this.suitoffsety}mm;
                    left: ${this.pos.x + this.width - this.suitWidth - this.suitoffsetx}mm;
                    font-size: ${this.letterheight}mm;
                    height: ${this.letterheight}mm;
                    width: ${this.suitWidth}mm;
                    rotate: 180deg
                `
                return ret;
            } else {
                return ""
            }
        },
        bottomSuitStyle() {
            if (this.pos != undefined && this.suitoffsetx != undefined && this.suitoffsety != undefined) {
                var ret = `
                    position: absolute;
                    top: ${this.pos.y + this.height - this.suitWidth - this.suitoffsety}mm;
                    left: ${this.pos.x + this.width - this.suitWidth - this.suitoffsetx}mm;
                    rotate: 180deg;
                `
                return ret;
            } else {
                return ""
            }
        },
        position() {
            return this.getAbsPos(document.getElementById('card-' + this.unique));
        },
        numberBoxX() {
            var ret = this.pos.x + (this.width - this.numberboxwidth)/2;
            console.log(ret, this.pos.x, this.width, this.numberboxwidth);
            return ret;
        },
        numberBoxY() {
            return this.pos.y + (this.height - this.numberboxheight)/2;
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
