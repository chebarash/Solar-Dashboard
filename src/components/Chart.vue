<template>
    <article class="chart">
        <span>
            <p class="value">
                <slot></slot>
            </p>
            <p class="title">
                <slot name="title"></slot>
            </p>
        </span>
        <div>скоро</div>
        <svg>
            <defs>
                <pattern id="grid" width="50" height="50" patternUnits="userSpaceOnUse">
                    <path d="M 50 0 L 0 0" fill="none" stroke="var(--color-divider)" stroke-width="1"></path>
                </pattern>
            </defs>
            <rect x="30" width="100%" height="calc(100% - 45px)" fill="url(#grid)"></rect>
            <g class="date">
                <text x="150" y="370">20</text>
                <text x="250" y="370">21</text>
                <text x="350" y="370">22</text>
                <text x="450" y="370">23</text>
                <text x="550" y="370">24</text>
                <text x="650" y="370">25</text>
                <text x="750" y="370">26</text>
            </g>
            <g class=" week">
                <text x="150" y="350">ПН</text>
                <text x="250" y="350">ВТ</text>
                <text x="350" y="350">СР</text>
                <text x="450" y="350">ЧТ</text>
                <text x="550" y="350">ПТ</text>
                <text x="650" y="350">СБ</text>
                <text x="750" y="350">ВС</text>
            </g>
            <g class="num">
                <text x="20" y="5">6k</text>
                <text x="20" y="55">5k</text>
                <text x="20" y="105">4k</text>
                <text x="20" y="155">3k</text>
                <text x="20" y="205">2k</text>
                <text x="20" y="255">1k</text>
                <text x="20" y="305">0k</text>
            </g>
            <linearGradient id="grad" x1="0%" y1="0%" x2="0%" y2="100%">
                <stop stop-color="var(--color-chart-bg)" />
                <stop offset="1" stop-color="var(--color-chart-bg)" stop-opacity="0" />
            </linearGradient>
            <path d="
            M 50 0
            L 150 100
            L 250 80
            L 350 160
            L 450 100
            L 550 100
            L 650 170
            L 750 200" fill="none" stroke="var(--color-chart)" stroke-width="3"></path>
            <path d="
            M 50 300
            L 50 0
            L 150 100
            L 250 80
            L 350 160
            L 450 100
            L 550 100
            L 650 170
            L 750 200
            L 750 300 Z" fill="url(#grad)" />
        </svg>
    </article>
</template>

<style scoped>
.chart {
    min-height: 380px;
    border-radius: var(--radius);
    background: var(--color-background-soft);
    padding: 45px 50px;
    gap: 30px;
    display: grid;
    grid-template-rows: auto 1fr;
    position: relative;
}

.chart span {
    display: flex;
    align-items: center;
    gap: 25px;
}

.title {
    color: var(--color-text);
    font-size: 20px;
    font-weight: 400;
    width: min-content;
}

.value {
    color: var(--color-heading);
    font-size: 70px;
    font-weight: 700;
}

svg {
    width: 100%;
    height: 100%;
    filter: blur(10px);
}

.week,
.date {
    text-anchor: middle;
}

.num {
    text-anchor: end;
}

.num text {
    fill: var(--color-heading);
    font-size: 14px;
    font-weight: 500;
}

.week text {
    fill: var(--color-slider);
    font-size: 14px;
    font-weight: 400;
}

.date text {
    fill: var(--color-heading);
    font-size: 17px;
    font-weight: 500;
}

div {
    position: absolute;
    display: flex;
    align-items: center;
    justify-content: center;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    font-size: 50px;
    font-weight: 700;
    z-index: 99;
    opacity: .8;
}

@media (max-width: 720px) {
    .chart {
        padding: 20px;
        gap: 20px;
    }

    .chart span {
        flex-direction: column;
        align-items: flex-start;
        gap: 0px;
    }

    .title {
        font-size: 16px;
        width: max-content;
    }

    .value {
        font-size: 40px;
    }
}
</style>
