<script setup lang="ts">
const props = defineProps<{ imports: Array<{ date: Date; icons: Array<string> }> }>()

type Coord = { x: number, y: number }


const count: { [date: string]: number } = {};

const getDateString = (date: Date) => new Date(date).toLocaleString('en-GB').split(`, `)[0]

props.imports.map(({ date }) => getDateString(date)).forEach(date => {
    count[date] = (count[date] || 0) + 1;
});

const now = Date.now()
const day = 1000 * 60 * 60 * 24

const week: Array<[{ weekday: string, date: number }, number]> = []
const maxValue = Math.max(...Object.values(count))

for (let i = 7; i >= 0; i--) {
    const date = new Date(now - day * i)
    week.push([{ weekday: date.toLocaleString('ru', { weekday: 'short', }), date: date.getDate() }, count[getDateString(date)] || 0])
}

const f = 0.3
const t = 0.6

const strokeColor = getComputedStyle(document.documentElement)
    .getPropertyValue('--color-chart');
const bgColor = getComputedStyle(document.documentElement)
    .getPropertyValue('--color-chart-bg');

const gradient = (a: Coord, b: Coord) => (b.y - a.y) / (b.x - a.x)

const draw = (ctx: CanvasRenderingContext2D, points: Array<Coord>) => {
    let dx1 = 0, dy1 = 0, dx2 = 0, dy2 = 0;
    let preP = points[0];

    for (let i = 1; i < points.length; i++) {
        const curP = points[i];
        const nexP = points[i + 1];

        if (nexP) {
            dx2 = (nexP.x - curP.x) * -f;
            dy2 = dx2 * gradient(preP, nexP) * t;
        } else {
            dx2 = 0;
            dy2 = 0;
        }

        ctx.bezierCurveTo(
            preP.x - dx1, preP.y - dy1,
            curP.x + dx2, curP.y + dy2,
            curP.x, curP.y
        );

        dx1 = dx2;
        dy1 = dy2;
        preP = curP;
    }
}

const chart = () => {
    const canvas = document.querySelector("canvas");
    const box = document.querySelector(".chart");
    if (!canvas || !box) return;

    canvas.width = box.clientWidth - 50;
    canvas.height = box.clientHeight - 90;



    const step = canvas.width / 7;
    const points: Array<Coord> = week.map(([_, value], i) => ({ x: i * step, y: (1 - value / maxValue) * (canvas.height - 10) + 5 }))

    const bg = canvas.getContext("2d");
    if (!bg) return
    bg.beginPath();
    const g = bg.createLinearGradient(0, 0, 0, canvas.height);
    g.addColorStop(0, bgColor);
    g.addColorStop(1, `${bgColor.slice(0, 7)}00`);
    bg.fillStyle = g
    bg.moveTo(0, canvas.height);
    bg.lineTo(points[0].x, points[0].y);
    draw(bg, points)
    bg.lineTo(canvas.width, canvas.height);
    bg.fill()

    const line = canvas.getContext("2d");
    if (!line) return
    line.lineWidth = 4;
    line.strokeStyle = strokeColor
    line.beginPath();
    line.moveTo(points[0].x, points[0].y);
    draw(line, points)
    line.stroke();
}

const vChart = {
    mounted: () => {
        window.addEventListener('resize', chart)
        chart()
    }
}
</script>

<template>
    <div class="chart">
        <canvas v-chart></canvas>
        <div class="graph">
            <div class="vertical">
                <ul class="values">
                    <li v-for="index in 4">
                        <p class="value">{{ +(maxValue / 3 * (index - 1)).toFixed(1) }}</p>
                    </li>
                </ul>
                <ul class="lines">
                    <li v-for="_ in 4"></li>
                </ul>
            </div>
            <ul class="horizontal">
                <li v-for="([{ weekday, date }], index) of week">
                    <p class="weekday" v-if="index">{{ weekday }}</p>
                    <p class="date" v-if="index">{{ date }}</p>
                </li>
            </ul>
        </div>
    </div>
</template>

<style scoped>
.chart {
    position: relative;
}

canvas {
    position: absolute;
    right: 10px;
}

.graph {
    height: 100%;
    display: grid;
    grid-template-rows: 1fr auto;
    gap: 35px;
}


.vertical {
    flex: 1;
    display: flex;
    gap: 20px;
}

.values {
    display: flex;
    flex-direction: column-reverse;
    align-items: flex-end;
    justify-content: space-between;
    width: min-content;
}

.lines {
    display: flex;
    flex-direction: column-reverse;
    align-items: flex-end;
    justify-content: space-between;
    width: 100%;
    padding: 9px 0
}

.lines li {
    width: 100%;
    height: 3px;
    background: var(--color-divider);
}

.horizontal {
    display: flex;
    align-items: flex-end;
    justify-content: space-between;
    height: 45px;
    padding-left: 30px;
}

.horizontal li {
    width: 20px;
}

li {
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    gap: 6px;
}

.value {
    color: var(--color-text);
    font-size: 14px;
    font-weight: 500;
}

.weekday {
    color: var(--color-text);
    font-size: 14px;
    font-weight: 400;
}

.date {
    color: var(--color-heading);
    font-size: 17px;
    font-weight: 500;
}

@media (max-width: 720px) {

    .value {
        font-size: 12px;
    }

    .weekday {
        font-size: 12px;
    }

    .date {
        font-size: 14px;
    }
}
</style>