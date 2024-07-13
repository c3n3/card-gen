<script></script>
<template>
    <div class="surround" :style="getBox()">
        <img v-for="i in 10" v-bind:key="i" :src="suitimage" :width="mmToPx(this.imagewidth)" :height="mmToPx(this.imagewidth)" class="suit" :style="getNumberStyle(i-1)"/>
    </div>
</template>

<style scoped>
.surround {
    /* border: 1px solid black; */
    position: absolute;
}
</style>
<script>

// These are all done by percents
// They will be translated by the card directly
var nums = {
    '6': [
        {x: 0, y: 0},
        {x: 1, y: 0},
        {x: 0, y: 0.5},
        {x: 1, y: 0.5},
        {x: 0, y: 1},
        {x: 1, y: 1},
    ],
    '7': [
        {x: 0, y: 0},
        {x: 1, y: 0},
        {x: 0, y: 0.5},
        {x: 1, y: 0.5},
        {x: 0, y: 1},
        {x: 1, y: 1},
        {x: 0.5, y: 0.25},
    ],
    '10': [
        {x: 0, y: 0},
        {x: 0, y: 0.33},
        {x: 0, y: 0.66},
        {x: 0, y: 1},

        {x: 1, y: 0},
        {x: 1, y: 0.33},
        {x: 1, y: 0.66},
        {x: 1, y: 1},

        {x: 0.5, y: 0.33/2},
        {x: 0.5, y: 1-0.3/2},
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
            console.log("Positions", Number(this.height) * percent.y, Number(this.y), Number(this.imagewidth), xPos, yPos);
            return {
                x: xPos,
                y: yPos,
            }
        },
        getNumberStyle(num) {
            var placements = nums[String(num)];
            if (num >= placements.length) {
                console.log("REJECTION", num);
                return "display: none;";
            }
            var pos = this.toPosition(placements[num]);
            var ret = `
                position: absolute;
                top: ${pos.y}mm;
                left: ${pos.x}mm;
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
            console.log(ret);
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
