<script></script>
<template>
    <div class="surround" :style="getBox()">
        <div v-for="i in 11" v-bind:key="i + number">
            <img :id="number + i + x + y"
                 v-if="suitimage"
                 :src="suitimage"
                 :width="mmToPx(this.imagewidth)"
                 :height="mmToPx(this.imagewidth)"
                 class="suit"
                 :style="getNumberStyle(i-1)"/>
        </div>
    </div>
</template>

<style scoped>
.surround {
    /* border: 1px solid black; */
    position: relative;
}
</style>
<script>

// These are all done by percents
// They will be translated by the card directly
var nums = {
    'A': [
        {x: 0.5, y: 0.5, r: 0}
    ],
    2: [
        {x: 0.5, y: 0, r: 0},
        {x: 0.5, y: 1, r: 180},       
    ],
    3: [
        {x: 0.5, y: 0, r: 0},
        {x: 0.5, y: 0.5, r: 0},
        {x: 0.5, y: 1, r: 180},
    ],
    4: [
        {x: 0, y: 0, r: 0},
        {x: 1, y: 0, r: 0},
        {x: 0, y: 1, r: 180},
        {x: 1, y: 1, r: 180},
    ],
    5: [
        {x: 0, y: 0, r: 0},
        {x: 1, y: 0, r: 0},
        {x: 0.5, y: 0.5, r: 0},
        {x: 0, y: 1, r: 180},
        {x: 1, y: 1, r: 180},
    ],
    6: [
        {x: 0, y: 0, r: 0},
        {x: 1, y: 0, r: 0},
        {x: 0, y: 0.5, r: 0},
        {x: 1, y: 0.5, r: 180},
        {x: 0, y: 1, r: 180},
        {x: 1, y: 1, r: 180},
    ],
    7: [
        {x: 0, y: 0, r: 0},
        {x: 1, y: 0, r: 0},
        {x: 0, y: 0.5, r: 0},
        {x: 1, y: 0.5, r: 180},
        {x: 0, y: 1, r: 180},
        {x: 1, y: 1, r: 180},
        {x: 0.5, y: 0.25, r: 0},
    ],
    8: [
        {x: 0, y: 0, r: 0},
        {x: 1, y: 0, r: 0},
        {x: 0, y: 0.5, r: 0},
        {x: 1, y: 0.5, r: 180},
        {x: 0, y: 1, r: 180},
        {x: 1, y: 1, r: 180},
        {x: 0.5, y: 0.25, r: 0},
        {x: 0.5, y: 0.75, r: 180},
    ],
    9: [
        {x: 0, y: 0, r: 0},
        {x: 0, y: 0.33, r: 0},
        {x: 0, y: 0.66, r: 180},
        {x: 0, y: 1, r: 180},

        {x: 1, y: 0, r: 0},
        {x: 1, y: 0.33, r: 0},
        {x: 1, y: 0.66, r: 180},
        {x: 1, y: 1, r: 180},

        {x: 0.5, y: 0.5, r: 0},
    ],
    10: [
        {x: 0, y: 0, r: 0},
        {x: 0, y: 0.33, r: 0},
        {x: 0, y: 0.66, r: 180},
        {x: 0, y: 1, r: 180},

        {x: 1, y: 0, r: 0},
        {x: 1, y: 0.33, r: 0},
        {x: 1, y: 0.66, r: 180},
        {x: 1, y: 1, r: 180},

        {x: 0.5, y: 0.33/2, r: 0},
        {x: 0.5, y: 1-0.3/2, r: 180},
    ],
}

export default {
    name: 'Numbers',
    props: [
        'suitimage',
        'number',
        "x",
        "y",
        "width",
        "height",
        "imagewidth"
    ],
    data() {
        return {}
    },
    methods: {
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
        toPosition(percent) {
            // Find the x distance
            var xPos = Number(this.width) * percent.x - Number(this.imagewidth)/2;
            var yPos = Number(this.height) * percent.y - Number(this.imagewidth)/2;
            return {
                x: xPos,
                y: yPos,
            }
        },
        getNumberStyle(num) {
            if (!num in nums || !this.suitimage) {
                return "display: none;";
            }
            var placements = nums[this.number];
            if (!placements) {
                return "display: none;";
            }
            if (num >= placements.length) {
                return "display: none;";
            }
            var pos = this.toPosition(placements[num]);
            var ret = `
                position: absolute;
                top: ${pos.y}mm;
                left: ${pos.x}mm;
                rotate: ${placements[num].r}deg;
            `;
            return ret;
        },
        getBox() {
            var ret = `
                position: absolute;
                top: ${this.y}mm;
                left: ${this.x}mm;
                width: ${this.width}mm;
                height: ${this.height}mm;
            `;
            return ret;
        }
    },
    mounted() {
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
