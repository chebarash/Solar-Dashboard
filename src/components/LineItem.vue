<script setup lang="ts">
import { ref, onMounted } from 'vue';
const props = defineProps<{ imports: Array<{ date: Date; icons: Array<string> }> }>()

type Coord = { x: number, y: number }

const d = ref<string>(``);
const bg = ref<string>(``);
const hint = ref<Coord>({ x: 0, y: 0 });
const height = ref<number>(0);
const width = ref<number>(0);

const count: { [date: string]: number } = {};

const getDateString = (date: Date) => new Date(date).toLocaleString('en-GB').split(`, `)[0]
const getUnit = (n: number) => `${n} time${n !== 1 ? `s` : ``}`

props.imports.forEach(({ date }) => {
    const name = getDateString(date)
    count[name] = (count[name] || 0) + 1;
});

const now = Date.now()
const day = 1000 * 60 * 60 * 24

const week: Array<[{ weekday: string, date: number }, number]> = []
const maxValue = Math.max(...Object.values(count))

for (let i = 7; i >= 0; i--) {
    const date = new Date(now - day * i)
    week.push([{ weekday: date.toLocaleString('en', { weekday: 'short', }), date: date.getDate() }, count[getDateString(date)] || 0])
}

const chart = () => {
    const box = document.querySelector(".box");
    if (!box) return;

    width.value = box.clientWidth - 50;
    height.value = box.clientHeight - 90;

    const step = width.value / 7;
    const points: Array<Coord> = week.map((day, i) => ({ x: i * step, y: (1 - day[1] / maxValue) * (height.value - 10) + 5 }))

    d.value = `M ${points[0].x} ${points[0].y}`
    bg.value = `M ${0} ${height.value}\nL ${points[0].x} ${points[0].y}`
    let dx1 = 0, dx2 = 0;
    let preP = points[0];

    for (let i = 1; i < points.length; i++) {
        const curP = points[i];
        const nexP = points[i + 1];

        if (nexP) dx2 = (nexP.x - curP.x) * -0.5;
        else dx2 = 0;

        const string = `\nC ${preP.x - dx1} ${preP.y}, ${curP.x + dx2} ${curP.y}, ${curP.x} ${curP.y}`

        d.value += string
        bg.value += string

        dx1 = dx2;
        preP = curP;
    }

    bg.value += `L ${width.value} ${height.value}`
}

const findY = (x: number) => {
    const path = document.querySelector(`path`)
    if (!path) return 0;
    const pathLength = path.getTotalLength()
    let start = 0, end = pathLength, target = (start + end) / 2;

    x = Math.max(x, path.getPointAtLength(0).x)
    x = Math.min(x, path.getPointAtLength(pathLength).x)

    while (target >= start && target <= pathLength) {
        const pos = path.getPointAtLength(target)
        if (Math.abs(pos.x - x) < 0.001) {
            return pos.y
        } else if (pos.x > x) {
            end = target
        } else {
            start = target
        }
        target = (start + end) / 2
    }

    return target
}

onMounted(() => {
    window.addEventListener('resize', chart)
    chart()
})
</script>

<template>
    <div class="box">
        <svg class="line" @mousemove="(e) => {
            const rect = (e.target as HTMLElement).getBoundingClientRect()
            const x = e.clientX - rect.left
            hint = { x, y: findY(e.clientX - rect.left) }
        }">
            <path fill="url('#bg')" :d="bg" />
            <path fill="none" :d="d" stroke-width="4" stroke="var(--color-chart)" stroke-linecap="round" />
            <defs>
                <linearGradient id="bg" gradientTransform="rotate(90)">
                    <stop offset="0" stop-color="var(--color-chart-bg-1)" />
                    <stop offset="100%" stop-color="var(--color-chart-bg-2)" />
                </linearGradient>
            </defs>
        </svg>
        <div class="hint" :style="{ top: `${hint.y - 66 + 7}px`, left: `${hint.x + 40}px` }">
            <p>{{ getUnit(Math.round((1 - hint.y / height) * maxValue)) }}</p>
            <svg width="18" height="8" viewBox="0 0 18 8" fill="none">
                <path d="M9 8C5.5 8 4 0 0 0H18C14 0 12.5 8 9 8Z" fill="var(--blue)" />
            </svg>
            <div></div>
        </div>
        <div class="graph">
            <div class="vertical">
                <ul class="values">
                    <li v-for="index in 4" :key="index">
                        <p class="value">{{ +(maxValue / 3 * (index - 1)).toFixed(1) }}</p>
                    </li>
                </ul>
                <ul class="lines">
                    <li v-for="index in 4" :key="index"></li>
                </ul>
            </div>
            <ul class="horizontal">
                <li v-for="( [{ weekday, date }], index ) of  week" :key="index">
                    <p class="weekday" v-if="index">{{ weekday }}</p>
                    <p class="date" v-if="index">{{ date }}</p>
                </li>
            </ul>
        </div>
    </div>
</template>

<style scoped>
.box {
    position: relative;
}

.hint {
    opacity: 0;
    position: absolute;
    transition: opacity linear 100ms;
    z-index: 99;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    pointer-events: none;
    transform: translate(-50%, 0);
}

.hint div {
    margin-top: 6px;
    width: 14px;
    height: 14px;
    border-radius: 100%;
    border: 3px solid var(--color-chart);
    background: var(--color-background-soft);
}

.hint p {
    color: var(--text-dark-1);
    font-size: 18px;
    font-weight: 500;
    border-radius: 20px;
    background: var(--blue);
    padding: 8px 10px;
    width: max-content;
}

.line:hover~.hint {
    opacity: 1;
}

.line {
    cursor: crosshair;
    position: absolute;
    top: 0;
    right: 10px;
    bottom: 90px;
    left: 40px;
    width: calc(100% - 50px);
    height: 100%;
}

path {
    transition: none;
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
    color: var(--color-heading);
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